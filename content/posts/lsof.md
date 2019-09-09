---
title: "Lsof"
date: 2019-09-08T21:15:14-07:00
---

# LSOF (list open files)
Everything is a file in Linux!  Run this to see all the names of the programs that are running:
```
lsof | awk '{print $1}' | sort | uniq
```

Then run this to see all the open file descriptors each program is using:
```
lsof | awk '{print $1}' | sort
```


Show all entries for a specific program:
```
lsof -c firefox | sort
```

Limit the entries to the first 10:
```
lsof -c firefox | sort | head -10
```