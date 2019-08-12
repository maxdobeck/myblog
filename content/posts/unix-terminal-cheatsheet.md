---
title: "Unix Terminal Cheatsheet"
date: 2019-08-11T22:52:00-07:00
draft: false
tags:
  - unix
  - cheatsheet
  - bash
  - cli
  - terminal
---

# Cheatsheet (Mostly for Bash)
I am still looking for the exact name of the terminal line where you enter commands.  For now I will refer to it below as `$` or the `Active Terminal Line (Terminal)`.

### Moving around the Active Terminal Line
ctrl + a :: move to beginning of line

ctrl + e :: move to end of  line

ctrl + f :: move fwd one char

alt + f :: move fwd one word

ctrl + b :: move back one char

alt + b :: move back one word

ctrl +xx :: swap between start of line and current position

### Deletion

ctrl + u :: delete everything to left of cursor

ctrl + k :: delete  everything to right of cursor

ctrl + w :: del one word to left of cursor

ctrl + c :: cancel (SIGINT) terminal line

ctrl + y :: undo 

alt + shift + # :: comment it but do nothing except move it into history.  returns a new term. line

### Piping
`$cat somefile.txt | wc -l` :: pipes output of 'cat' into 'wc', thus counting the lines in 'somefile.txt'.

### Sources and further reading
http://teohm.com/blog/shortcuts-to-move-faster-in-bash-command-line/
https://stackoverflow.com/questions/9679776/how-do-i-clear-delete-the-current-line-in-terminal
