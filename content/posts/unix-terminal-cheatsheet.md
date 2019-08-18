---
title: "Unix Terminal Cheatsheet"
date: 2019-08-11T22:52:00-07:00
draft: false
tags:
  - linux
  - cheatsheet
  - bash
  - cli
  - terminal
---

# Cheatsheet (Mostly for Bash)
Always add this to your google searches when trying to get something to work: [nixCraft](https://www.cyberciti.biz/).  Great site.

This is primarly for the Bash shell and the soon to be widespread Fish shell.  Not all of these will work on Mac even though its typically categorized as a *nix system.

### Moving around the Active Command Line
ctrl + a :: move to beginning of line

ctrl + e :: move to end of  line

ctrl + f :: move fwd one char

alt + f :: move fwd one word

ctrl + b :: move back one char

alt + b :: move back one word

ctrl + xx :: swap between start of line and current positions

### Deletion and Cutting from Active Command Line
ctrl + d :: delete the character under cursor

alt + d :: delete characters to the right of the cursor

ctrl + h :: delete one character to the left of the cursor

ctrl + y :: paste what was cut/copied from commands in the terminal.  May not use your OS clipboard

ctrl + u :: cut everything to left of cursor

ctrl + k :: cut  everything to right of cursor

ctrl + w :: cut one word to left of cursor

ctrl + c :: cancel (SIGINT) terminal line.  Usually used to kill the program running in the foreground

alt + shift + # :: comment it but do nothing except move it into history.  returns a new terminal line

### Editing Characters on Active Command Line
alt + t :: swap current word under cursor with previous word.  Will preserve spaces, commas and other line separators

ctrl + t :: swap current letter under cursor with left adjacent letter

alt + u :: capitalize characters to the right of the cursor.  Stops at end of word, linebreak, space, hyphen, etc

alt + l :: downcase characters to the right of the cursor. Stops at end of word, linebreak, space, hyphen, etc

alt + c :: capitalize the character under the cursor

### Changing Directories
`cd ..` :: move up one directory if possible.  Can be extended infinitely i.g. `$ cd ../../..` moves you up three directories.

`cd -` :: swap to last directory moved to with `cd` command.  If you `cd ~/java-programs` then `cd ~/scripts` you can use `cd -` to swap between the two directores ~/java-programs and ~/scripts.

### cat (concatenate)
`cat some-file.txt` :: prints the file "some-file.txt" to STDOUT out, so to your terminal in most cases.  Will output nonsense binary and symbols if the terminal doesn't have a way to represent the output.  Typically used to quickly scan the file contents without leaving the terminal or to pipe the `cat` output to another tool.

### Piping
`$ cat somefile.txt | wc -l` :: sends output of 'cat' into 'wc', thus counting the lines in 'somefile.txt'.  For comparison run `$ cat somefile.txt` and then run `$ cat somefile.txt | wc -l`.  Or if there's a specific word in your text file try `$ cat somefile.txt | grep someword`.

### Environment Variables
https://en.m.wikipedia.org/wiki/Environment_variable
Wikipedia says it best. Basically it’s a string that’s stored on the system running the program. Whatever local computer you are on will have different environment variables. Most Linux systems use the Bash shell and Bash reads the variables from .bashrc or .bash_profile usually. 

Each user on the system has separate environment variables. This is why it’s important to be clear about how these variables are created. Typically you’ll find that you use some sort of external system to manage environment variables as you move programs through testing, deployment, and multiple systems.

Create environment variable `export MYVAR='some-value'`  
Check the value of an env. variable `echo $MYVAR` 
Add environment variable permanently to ~/.bashrc.  `echo export MYVAR='some-string' >> ~/.bashrc`

### Sources and further reading
* cat man page https://linux.die.net/man/1/cat

* http://teohm.com/blog/shortcuts-to-move-faster-in-bash-command-line/
* https://stackoverflow.com/questions/9679776/how-do-i-clear-delete-the-current-line-in-terminal
* https://www.cyberciti.biz/ (nixCraft blog mentioned above)

Check your local library for these books first!  Nothing wrong with being an edition or two behind when the subject matter is fairly old:

* Linux in a nutshell - Ellen Siever, Stephen Figgins, Robert Love & Arnold Robbins :: [amazon link](https://www.amazon.com/0596154488-9780596154486-Linux-Nutshell-Paperback/dp/B07L5BZDB2/ref=sr_1_2?keywords=linux+in+a+nutshell+sixth+edition&qid=1565670362&s=gateway&sr=8-2)
* Learning the bash shell - Cameron Newham & Bill Rosenblatt :: [amazon link](https://www.amazon.com/Learning-bash-Shell-Programming-Nutshell/dp/0596009658/ref=sr_1_2?keywords=bash+learning+the+bash+shell&qid=1565670350&s=gateway&sr=8-2) 
* A Practical Guide to Linux Commands, Editors, and Shell Programming - Mark Sobell, Matthew Helmke [amazon link](https://www.amazon.com/Practical-Guide-Commands-Editors-Programming/dp/0134774604/ref=sr_1_1?keywords=sobell+linux+commands+editors%2C+and+shell+programming&qid=1565670716&s=gateway&sr=8-1)

