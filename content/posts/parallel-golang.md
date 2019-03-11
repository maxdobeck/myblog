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

#### Serial execution of a non-concurrent design

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


#### Serial program with go keyword thrown in

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

#### Serial program with go keyword and sleeps

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

#### Concurrent program with parallel goroutines

1. [Wait Groups](https://golang.org/pkg/sync/#WaitGroup)
2. [Channels](https://tour.golang.org/concurrency/2)

Here's some example [Stack Overflow answers](https://stackoverflow.com/questions/18207772/how-to-wait-for-all-goroutines-to-finish-without-using-time-sleep) and some [examples of Goroutines](https://gobyexample.com/goroutines).

1. Wait Groups
This will add the goroutine to a Wait Group and when the goroutine finishes it will report this to the Wait Group and the main process can exit.  Basically a lock or semaphore.  

```
 A WaitGroup waits for a collection of goroutines to finish. 
 The main goroutine calls Add to set the number of goroutines to wait for. 
 Then each of the goroutines runs and calls Done when finished. 
 At the same time, Wait can be used to block until all goroutines have finished. 
 ```

**Wait Group Example**

https://play.golang.org/p/tljB4g9C988

```
package main

import (
	"fmt"
	"math/rand"
	"time"
	"sync"
)

func main() {
  var wg sync.WaitGroup
	fmt.Println("Starting...")
	wg.Add(1)
	go worker("sort data", &wg)
	wg.Add(1)
	go worker("parse files", &wg)
	wg.Add(1)
	go worker("generate output", &wg)
	wg.Wait()
}

func worker(task string,  wg *sync.WaitGroup) {
	for i := 0; i < 3; i++ {
		time.Sleep(time.Second * time.Duration(rand.Intn(4)))
		fmt.Printf("%s %d complete\n", task, i)
	}
	wg.Done()
}
```
```
Starting...
generate output 0 complete
sort data 0 complete
parse files 0 complete
sort data 1 complete
generate output 1 complete
generate output 2 complete
sort data 2 complete
parse files 1 complete
parse files 2 complete

Program exited.
```

While the `WaitGroup` option is nice it does force us to add some dependencies.  We have to first add an argument to our `worker` function then we have to add a `Done` call to the `worker` function.  On top of that we'll need to instantiate an actual `WaitGroup` object that can then be used to make another call to `Add`, which again we have to do for each Go Routine.

This is nice but involves some forethough in your design to accomadate all the calls to the sync package.

A more succint example: https://play.golang.org/p/_GaPFAbn4Gp.

2. Channels
A good simple example: https://gobyexample.com/channels.  A [buffered channel example](https://tour.golang.org/concurrency/3), which just blocks a little less but we'll ignore that for now.  

Channels are basically pipes that you shove data into or pull data from.  They have primitive types like String, Int, etc so be careful there.  Overall they're not too tricky but here's a few places you can go wrong.

**Bad output**

https://play.golang.org/p/8c8GAQMkXpU
```
Starting...
0x432080
```

1. This hex output or whatever isn't useful
1. We are only printing out 1 thing?  What?

For item 1 we can simply use the channel properly and add `<-` that arrow symbol.  If you print `ch` directly you'll be printing the address of the channel object I believe.  The `<-` is a key symbol indicating you want to pop off whatever's on the channel.  So we add this `	fmt.Println(<-ch)` and we get this:
```
Starting...
generate output 0 complete
```
Much nicer!

For item 2 we'll obviously need to loop over the channel to continuously print everything that comes in.  So lets add that loop and see what happens!  [FYI here's a good looping example.](https://tour.golang.org/concurrency/4)

Added to `main()`.  https://play.golang.org/p/KCuD3QN2BfK
```
for i:= range ch {
  fmt.Print(i)
}
```

```
Starting...
generate output 0 complete
sort data 0 complete
parse files 0 complete
sort data 1 complete
generate output 1 complete
generate output 2 complete
sort data 2 complete
parse files 1 complete
parse files 2 complete
fatal error: all goroutines are asleep - deadlock!

goroutine 1 [chan receive]:
main.main()
	/tmp/sandbox688680220/main.go:16 +0x220
```

This output isn't great because we get a Deadlock warning.  I mean it is good because Golang is good about telling you when you're shooting yourself in the foot but its bad because we basically have a working program.  So what's wrong?  We're not closing the channel when we're done.  So the goroutines just idle and essentially force the main process to wait on them even though we're not sending any data to the reciever (the main process).  So lets close that from the senders side.

**Closing the channel properly for multiple goroutines**
https://play.golang.org/p/HOCBbJAhJiF

```
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	ch := make(chan string)
	
	fmt.Println("Starting...")
	go worker("sort data", ch)
	go worker("parse files", ch)
	go worker("generate output", ch)
	for i:= range ch {
		fmt.Print(i)
	}
}

func worker(task string, ch chan string) {
	for i := 0; i < 3; i++ {
		time.Sleep(time.Second * time.Duration(rand.Intn(4)))
		out := fmt.Sprintf("%s %d complete\n", task, i)
		ch <- out
	}
	close(ch)
}
```

That's more like it!

```
Starting...
generate output 0 complete
sort data 0 complete
parse files 0 complete
sort data 1 complete
generate output 1 complete
generate output 2 complete

Program exited.
```

Wait a second... :/ 
How many line entries should we have?

**More design to ensure proper logic**

Unfortunately we're not printing everything. There should be 9 line entries after `Starting...` is printed but you'll notice we're a few short.  It turns out the `close(ch)` will close the whole channel whenever one of the workers finishes.  So the first to finish is the first to close the channel for everyone else.

We need to redesign how we do this task.  What are our options?

1. Use a `select` statement and call the function once in main: https://tour.golang.org/concurrency/5.  This will reduce the already fairly shitty method of writing out `worker(some-argument)` whenever we need to call a worker.  However it means rewriting worker and how we pass data to it.

2. Hardcode how many times we print from channel and shut main process no matter what. https://play.golang.org/p/K2jIC2kX1UD This is awful but hey it works fo an example!

