---
title: "Parallel Golang"
date: 2019-03-09T17:10:54-08:00
draft: true
---

# Concurrency and Parallelism in Golang
Start Here: [Concurrency Is Not Parallelism](https://youtu.be/cN_DpYBzKso?list=PLs3DDrYN-r7t27EbsVOpyKF-hQ2P-IqWU). The basics are simple, concurrent != parallel. The subtle goal of Golang is to get you designing programs that are concurrent.  This way you can run them in parallel (execute it more than one time) and magically everything is faster.  Really there is no magic and its all blood and sweat with a hefty upfront cost for the design time.

Making something run in parallel isn't as simple as just running n+1 of them.  Resources and interfaces can block execution for all other threads/runtimes and actually make the overall execution slower than running your program serially. What Golang allows you to do is create a "blueprint" for completing some task.  With this blueprint you can almost be guaranteed that when you need to speed up the execution time you can run multiple versions of it or enable the singular execution to parllelize subtasks.

## Goroutines
How can your initial program run multiple subtasks efficiently?  How do you split off work into paralle executions via your concurrent design? Threads?  Is this a multithreading thing?  Why not just use asynchronous node.js packages, python libraries, or [insert language or framework of choice]?

Well most of the other languages out there are pretty old, not that that's a bad thing!  But the designs of older languages carry over the habits of computers with less computing power.  Now we all have multicore hardware and we're living in the future but the hurdle is getting human beings to harness all that future power.

So instead of just throwing work into a thread and hoping the asynchronous logic checks out and doesn't hit a wall of diminishing returns, use Goroutines!  A goroutine is not a thread, conceptually its like a thread but its lighter weight thus you can have thousands or millions.  Really it comes back to [Communicating Sequential Processes](http://usingcsp.com/cspbook.pdf) but lots of smarter people have talked about what a goroutine actually is.  For more depth listen to that talk about [Concurrency not being Parallelism up above](https://youtu.be/cN_DpYBzKso?list=PLs3DDrYN-r7t27EbsVOpyKF-hQ2P-IqWU) and this talk about the [Go scheduler](https://youtu.be/YHRO5WQGh0k?list=PL2ntRZ1ySWBdatAqf-2_125H4sGzaWngM).

With all that boiler plate out of the way how can Go routinens benefit you?

## How to use Goroutines (Grokking Goroutines)
Alright.  You have a series of "workers" who do things.  Unfortunately it turns out BIG CUSTOMER needs them to be faster at doing things.  OK, what do you do with this:

**Serial execution of a non-concurrent design**

https://play.golang.org/p/7ZmPgLazUW1.
```
package main

import (
	"fmt"
)

func main() {
	fmt.Println("Starting...")
	worker("sort data")
	worker("parse files")
	worker("generate output")
}

func worker(task string) {
	for i := 0; i < 3; i++ {
		fmt.Printf("%s %d complete\n", task, i)
	}
}
```

```
Starting...
sort data 0 complete
sort data 1 complete
sort data 2 complete
parse files 0 complete
parse files 1 complete
parse files 2 complete
generate output 0 complete
generate output 1 complete
generate output 2 complete

Program exited.
```

Well logic dictates you just throw the keyword `go` in front of your function calls to `worker()`.  Lets try:


**Serial program with go keyword thrown in**

https://play.golang.org/p/hvlmBaE9YNy

Uhhh what?  Why didn't anything happen?
```
func main() {
	fmt.Println("Starting...")
	go worker("sort data")
	go worker("parse files")
	go worker("generate output")
}

func worker(task string) {
	for i := 0; i < 3; i++ {
		fmt.Printf("%s %d complete\n", task, i)
	}
}
```

```
Starting...

Program exited.
```

With a little googling and Stack Overflow help we learn:
`When the function main returns, the program exits. It does not wait for other (non-main) goroutines to complete.`  Ok so we have to wait for the goroutines?  How about a sleep?

**Serial program with go keyword and sleeps**

https://play.golang.org/p/AIfFaM7EaOA

OK here's our new `main()` and output. I also spiced up the `worker()` function to have a random time so the output "feels" more asynchronous.

```
func main() {
	fmt.Println("Starting...")
	go worker("sort data")
	go worker("parse files")
	go worker("generate output")
	time.Sleep(time.Second * 15)
}
```

```
Starting...
sort data 0 complete
parse files 0 complete
generate output 0 complete
parse files 1 complete
sort data 1 complete
sort data 2 complete
parse files 2 complete
generate output 1 complete
generate output 2 complete

Program exited.
```

So that feels pretty parallel!  But its not exactly "production ready" with the random time.sleep for 15 seconds.  How do we actually force the main process to wait for the goroutines?

**Concurrent program with parallel goroutines**

