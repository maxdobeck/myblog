---
title: "Awk Toolkit"
date: 2019-09-05T21:19:32-07:00
draft: false
---

# Awk
https://linux.die.net/man/1/awk

Official summary [via Wikipedia](https://en.wikipedia.org/wiki/AWK)

[133,000 stackoverflow questions](https://stackoverflow.com/search?q=awk) mention awk but if you skim them most of the mentions are as part of a bash script like `... awk '{s+=$1} END {print s}' ...` or `cat some-file.txt| awk -F'/' '{print $3 "\t" $5}' ...`.  You'll notice a lot of pipes `|` around scripts that use awk as most people typically pipe the output to something else, maybe a file, for use elsewhere.  Long story short, Awk is typically a stop along the way to something else.

Awk is commonly used like glue.  Most of the time you just need a chunk of data from a CSV file or maybe you need to sort some text data or weed out every value that doesn't repeat in a dataset.  Awk allows you to do this as it has conditional statements, loops, variables, and almost everything you can do in a typical programming language.

Great examples here:
https://www.cyberciti.biz/faq/bash-scripting-using-awk/
https://www.cyberciti.biz/faq/how-to-print-filename-with-awk-on-linux-unix/

and here:
https://likegeeks.com/awk-command/

# Important Info on the $2
$1 is the first column of data.  $0 is the whole row (all columns).  $2 is the second.  $3 is the second and so on.

# Samples
Here are a few examples, some contrived, some real world where I've seen or used Awk.
#### Print PID of all grepped processes
```
ps aux | grep firefox | grep -v grep | awk '{ print $2 }'
```

Output:

10667

10779

10856

11885

12095

12121

12227

13659

14003

14287

Here I'm using Firefox but it could be any process you can grep.

#### Kill a specific process
Lets extend the example above. We want to find a certain process and then stop it. Here's some random postrgess stuff running below.  

Lets say I want to kill the "stats collector process"
```
postgres  1663  0.0  0.0 320160  4948 ?        Ss   Sep04   0:00 postgres: 10/main: checkpointer process   
postgres  1664  0.0  0.0 320048  2312 ?        Ss   Sep04   0:01 postgres: 10/main: writer process   
postgres  1665  0.0  0.0 320048  7212 ?        Ss   Sep04   0:01 postgres: 10/main: wal writer process   
postgres  1668  0.0  0.0 320448  4288 ?        Ss   Sep04   0:01 postgres: 10/main: autovacuum launcher process   
postgres  1669  0.0  0.0 174972  1704 ?        Ss   Sep04   0:01 postgres: 10/main: stats collector process   
postgres  1670  0.0  0.0 320356  2764 ?        Ss   Sep04   0:00 postgres: 10/main: bgworker: logical replication launcher   
postgres  1675  0.0  0.0 303556  1340 ?        Ss   Sep04   0:00 postgres: checkpointer process   
postgres  1677  0.0  0.0 303556  3132 ?        Ss   Sep04   0:00 postgres: writer process   
postgres  1678  0.0  0.0 303556  1476 ?        Ss   Sep04   0:00 postgres: wal writer process   
postgres  1679  0.0  0.0 303860  2992 ?        Ss   Sep04   0:00 postgres: autovacuum launcher process   
postgres  1680  0.0  0.0 158540  1024 ?        Ss   Sep04   0:00 postgres: stats collector process   
```

So lets find it:
```
ps aux | grep "stats collector" | grep -v grep
```
Output:

postgres  1669  0.0  0.0 174972  1704 ?        Ss   Sep04   0:01 postgres: 10/main: stats collector process   
postgres  1680  0.0  0.0 158540  1024 ?        Ss   Sep04   0:00 postgres: stats collector process

And stop it by using a system call to kill:
```
ps aux | grep "stats collector" | grep -v grep | awk '{ print $2; system("sudo kill " $2)}'
```

Or by passing the output to kill as a variable:
```
sudo kill $(ps aux | grep "stats collector" | grep -v grep | awk '{ print $2}')
```

A better example may be Chrome, that's a safe one to try out yourself.

#### Find processes consuming a certain amount of file descriptors
If you're not familiar with file descriptors that's ok.  Just know that we don't want processes consuming too many file descriptors as we could end up with things crashing and breaking.

Lets make sure we can find our processes, lets use Chrome as an example:
```
ps aux | grep "chrome" | grep -v grep | wc -l
```

Then we'll put that in a for loop and check each iteration of the loop (the $i or PID) for the open file descriptors. 
```
echo "PID   FileDescriptors" && for i in `ps aux | awk '/chrome/ {print $2}'`; do if [ -d "/proc/$i/fd" ]; then echo -e "$i `ls -l /proc/$i/fd | wc -l`\n"; else echo -e "could not find file $i\n"; fi done
```
Output:

PID   FileDescriptors
18465 216

18476 16

18477 8

18480 17

18503 41

18506 51

18560 41

18602 31

18606 40

could not find file 29504

could not find file 29231



Here's the if else statement for readability:
```
if [ -d "/proc/$i/fd" ]; then echo -e "$i `ls -l /proc/$i/fd | wc -l`\n"; else echo -e "could not find file $i\n"; fi
```


