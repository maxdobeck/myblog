---
title: "Awk Toolkit"
date: 2019-09-05T21:19:32-07:00
draft: true
---

# AWK 
https://linux.die.net/man/1/awk

[133,000 stackoverflow questions](https://stackoverflow.com/search?q=awk) mention awk but if you skim them most of the mentions are as part of a bash script like ` awk '{s+=$1} END {print s}' ` or `| awk -F'/' '{print $3 "\t" $5}' `.  You'll notice a lot of pipes `|` around scripts that use awk as most people typically pipe the output to something else, maybe a file, for use elsewhere.  Awk is typically a stop along the way to something else.

It is used like glue.  Most of the time you just need a chunk of data from a CSV file or maybe you need to sort some text data or weed out every value that doesn't repeat in a dataset.  Awk allows you to do this as it has conditional statements, loops, variables, and almost everything you can do in a typical programming language.

Great examples here:
https://www.cyberciti.biz/faq/bash-scripting-using-awk/

and here:
https://likegeeks.com/awk-command/

