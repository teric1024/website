---
title: bandit walkthrough
description: over the wire bandit
layout: libdoc_page
date: 1111-11-11
eleventyNavigation:
    key: bandit OverTheWire CTFs
    parent: OverTheWire CTFs
    title: bandit walkthrough
    order: 30
---
## Bandit

https://overthewire.org/wargames/bandit/
Don’t panic! Don’t give up!

[A good walkthrough](https://medium.com/@theGirlWhoEncrypts)

### level 0
Connect to **bandit.labs.overthewire.org** on port 2220 with username **bandit0** and password **bandit0**.

#### Solution
  
  `$ bandit.labs.overthewire.org -p 2220 -l bandit0`


### level 0 → level 1
The password for the next level is stored in a file called **readme** located in the home directory. Use this password to log into bandit1 using [[SSH]].

#### Solution
  
  After logging into bandit0,
```sh=
bandit0@bandit:~$ ls
readme
bandit0@bandit:~$ cat readme
boJ9jbbUNNfktd78OOpsqOltutMc3MY1
```

#### Password
boJ9jbbUNNfktd78OOpsqOltutMc3MY1

### level 1 → level 2
The password for the next level is stored in a file called **-** located in the home directory.

#### Solution
  
  After logging into bandit1,
```sh=
bandit1@bandit:~$ ls
-
bandit1@bandit:~$ cat ./-
CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9
```

#### Password
CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9


### level 2 → level 3
The password for the next level is stored in a file called **spaces in this filename** located in the home directory.

#### Solution
  
After logging into bandit2,
```sh=
bandit2@bandit:~$ ls
spaces in this filename
bandit2@bandit:~$ cat "spaces in this filename"
UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK
```
#### Password
UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK


### level 3 → level 4
The password for the next level is stored in a hidden file in the **inhere** directory.

#### Solution
  
  After logging into bandit3,
```sh=
bandit3@bandit:~$ ls
inhere
bandit3@bandit:~$ cd inhere
bandit3@bandit:~/inhere$ ls -a
.  ..  .hidden
bandit3@bandit:~/inhere$ strings .hidden
pIwrPrtPN36QITSp3EQaw936yaFoFgAB
```
#### Password
pIwrPrtPN36QITSp3EQaw936yaFoFgAB

### level 4 → level 5
The password for the next level is stored in the only human-readable file in the **inhere** directory. Tip: if your terminal is messed up, try the “reset” command.
- Tips
    - Pipeline
    - `sed`
    - ` $() ` (backticks)

- Notes
    - [pipe vs command substitution](https://unix.stackexchange.com/questions/17107/process-substitution-and-pipe)

#### Solution
  
  After logging into bandit4,
```sh=
bandit4@bandit:~$ ls
inhere
bandit4@bandit:~$ cd inhere/
bandit4@bandit:~/inhere$ ls
-file00  -file01  -file02  -file03  -file04  -file05  -file06  -file07  -file08  -file09
bandit4@bandit:~/inhere$ strings $(ls -a | sed "s/-/.\/-/")
!TQO
koReBOKuIDDepwhWk7jZC0RTdopnAYKh
```
#### Password
koReBOKuIDDepwhWk7jZC0RTdopnAYKh


### level 5 → level 6
The password for the next level is stored in a file somewhere under the **inhere** directory and has all of the following properties:
1. human-readable
2. 1033 bytes in size
3. not executable


#### Solution
  
  After logging into bandit5,
```sh=
bandit5@bandit:~$ ls
inhere
bandit5@bandit:~$ cd inhere
bandit5@bandit:~/inhere$ ls
maybehere00  maybehere03  maybehere06  maybehere09  maybehere12  maybehere15  maybehere18
maybehere01  maybehere04  maybehere07  maybehere10  maybehere13  maybehere16  maybehere19
maybehere02  maybehere05  maybehere08  maybehere11  maybehere14  maybehere17
bandit5@bandit:~/inhere$ du -b -a . | grep "1033"
1033    ./maybehere07/.file2
bandit5@bandit:~/inhere$ cat ./maybehere07/.file2
DXjZPULLxYr17uwoI01bNLQbtFemEgo7
```
#### Password
DXjZPULLxYr17uwoI01bNLQbtFemEgo7

### level 6 → level 7
The password for the next level is stored somewhere on the server and has all of the following properties:
1. owned by user bandit7
2. owned by group bandit6
3. 33 bytes in size


#### Solution
  
  After logging into bandit6,
```sh=
bandit6@bandit:~$ ls
bandit6@bandit:~$ cd ..
bandit6@bandit:/home$ ls
bandit0   bandit12  bandit16  bandit2   bandit23  bandit27      bandit29      bandit30-git  bandit33  bandit7
bandit1   bandit13  bandit17  bandit20  bandit24  bandit27-git  bandit29-git  bandit31      bandit4   bandit8
bandit10  bandit14  bandit18  bandit21  bandit25  bandit28      bandit3       bandit31-git  bandit5   bandit9
bandit11  bandit15  bandit19  bandit22  bandit26  bandit28-git  bandit30      bandit32      bandit6
bandit6@bandit:/home$ du . -a -b | grep 33
du: cannot read directory './bandit28-git': Permission denied
33      ./bandit4/inhere/-file01
33      ./bandit4/inhere/-file00
33      ./bandit4/inhere/-file06
33      ./bandit4/inhere/-file03
33      ./bandit4/inhere/-file05
33      ./bandit4/inhere/-file08
du: cannot read directory './bandit30-git': Permission denied
33      ./bandit4/inhere/-file04
33      ./bandit4/inhere/-file07
33      ./bandit4/inhere/-file02
33      ./bandit4/inhere/-file09
33033   ./bandit8/data.txt
3526    ./bandit33/.bashrc
675     ./bandit33/.profile
220     ./bandit33/.bash_logout
430     ./bandit33/README.txt
8947    ./bandit33
33      ./bandit0/readme
du: cannot read directory './bandit5/inhere': Permission denied
33      ./bandit25/.bandit24.password
10233   ./bandit25
33      ./bandit15/.bandit14.password
33      ./bandit3/inhere/.hidden
du: 33  ./bandit16/.bandit15.password
33      ./bandit2/spaces in this filename
33      ./bandit1/-
cannot read directory './bandit27-git': Permission denied
du: 33  ./bandit18/readme
33      ./bandit21/.prevpass
3300    ./bandit17/passwords.old
33      ./bandit17/.bandit16.password
3300    ./bandit17/passwords.new
cannot read directory './bandit29-git': Permission denied
du: cannot read directory './bandit31-git': Permission denied
```
Nothing meets the property.
```sh=
bandit6@bandit:/home$ cd ../..
bandit6@bandit:/$ ls
bin      dev   initrd.img      lib32   lost+found  opt         root  share  tmp  vmlinuz
boot     etc   initrd.img.old  lib64   media       proc        run   srv    usr  vmlinuz.old
cgroup2  home  lib             libx32  mnt         README.txt  sbin  sys    var
bandit6@bandit:/$ du . -a -b -h | grep -e "33.*bandit7"
du: cannot read directory './root': Permission denied
du: cannot read directory './home/bandit28-git': Permission denied
du: cannot read directory './home/bandit30-git': Permission denied
du: cannot read directory './home/bandit5/inhere': Permission denied
du: cannot read directory './home/bandit27-git': Permission denied
du: cannot read directory './home/bandit29-git': Permission denied
du: cannot read directory './home/bandit31-git': Permission denied
du: cannot read directory './lost+found': Permission denied
33      ./etc/bandit_pass/bandit7
du: cannot read directory './etc/ssl/private': Permission denied
du: cannot read directory './etc/polkit-1/localauthority': Permission denied
du: cannot read directory './etc/lvm/archive': Permission denied
du: cannot read directory './etc/lvm/backup': Permission denied
du: cannot read directory './sys/fs/pstore': Permission denied
du: cannot read directory './proc/tty/driver': Permission denied
du: cannot access './proc/4221/task/4221/fd/4': No such file or directory
du: cannot access './proc/4221/task/4221/fdinfo/4': No such file or directory
du: cannot access './proc/4221/fd/3': No such file or directory
du: cannot access './proc/4221/fdinfo/3': No such file or directory
du: cannot read directory './cgroup2/csessions': Permission denied
du: cannot read directory './boot/lost+found': Permission denied
du: cannot read directory './tmp': Permission denied
du: cannot read directory './run/lvm': Permission denied
du: cannot read directory './run/screen/S-bandit0': Permission denied
du: cannot read directory './run/screen/S-bandit1': Permission denied
du: cannot read directory './run/screen/S-bandit12': Permission denied
du: cannot read directory './run/screen/S-bandit11': Permission denied
du: cannot read directory './run/screen/S-bandit10': Permission denied
du: cannot read directory './run/screen/S-bandit3': Permission denied
du: cannot read directory './run/screen/S-bandit29': Permission denied
du: cannot read directory './run/screen/S-bandit15': Permission denied
du: cannot read directory './run/screen/S-bandit8': Permission denied
du: cannot read directory './run/screen/S-bandit13': Permission denied
du: cannot read directory './run/screen/S-bandit19': Permission denied
du: cannot read directory './run/screen/S-bandit9': Permission denied
du: cannot read directory './run/screen/S-bandit27': Permission denied
du: cannot read directory './run/screen/S-bandit2': Permission denied
du: cannot read directory './run/screen/S-bandit14': Permission denied
du: cannot read directory './run/screen/S-bandit16': Permission denied
du: cannot read directory './run/screen/S-bandit22': Permission denied
du: cannot read directory './run/screen/S-bandit4': Permission denied
du: cannot read directory './run/screen/S-bandit31': Permission denied
du: cannot read directory './run/screen/S-bandit24': Permission denied
du: cannot read directory './run/screen/S-bandit21': Permission denied
du: cannot read directory './run/screen/S-bandit25': Permission denied
du: cannot read directory './run/screen/S-bandit23': Permission denied
du: cannot read directory './run/screen/S-bandit20': Permission denied
du: cannot read directory './run/shm': Permission denied
du: cannot read directory './run/lock/lvm': Permission denied
du: cannot read directory './var/spool/bandit24': Permission denied
du: cannot read directory './var/spool/cron/crontabs': Permission denied
du: cannot read directory './var/spool/rsyslog': Permission denied
du: cannot read directory './var/tmp': Permission denied
du: cannot read directory './var/lib/apt/lists/partial': Permission denied
du: cannot read directory './var/lib/polkit-1': Permission denied
33      ./var/lib/dpkg/info/bandit7.password
du: cannot read directory './var/log': Permission denied
du: cannot read directory './var/cache/apt/archives/partial': Permission denied
du: cannot read directory './var/cache/ldconfig': Permission denied
bandit6@bandit:/$ strings ./var/lib/dpkg/info/bandit7.password
HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs
```
#### Password
HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs


### level 7 → level 8
The password for the next level is stored in the file **data.txt** next to the word **millionth**.


#### Solution
  
  After logging into bandit7,
```sh=
bandit7@bandit:~$ ls
data.txt
bandit7@bandit:~$ strings data.txt | grep "millionth"
millionth       cvX2JJa4CFALtqS87jk27qwqGhBM9plV
```
#### Password
cvX2JJa4CFALtqS87jk27qwqGhBM9plV

### level 8 → level 9
The password for the next level is stored in the file **data.txt** and is the only line of text that occurs only once.

#### Solution
  
  After logging into bandit8,
```sh=
bandit8@bandit:~$ strings data.txt | sort | uniq -c | grep "1 "
      1 UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR
```
#### Password
UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR


### level 9 → level 10
The password for the next level is stored in the file **data.txt** in one of the few human-readable strings, preceded by several ‘=’ characters.

#### Solution
  
  After logging into bandit9,
```sh=
bandit9@bandit:~$ strings data.txt | grep "=="
========== the*2i"4
========== password
Z)========== is
&========== truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk
```
#### Password
truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk


### level 10 → level 11
The password for the next level is stored in the file **data.txt**, which contains base64 encoded data.

- Tips
    - == at the end -> base64

#### Solution
  
  After logging into bandit10,
```sh=
bandit10@bandit:~$ base64 -d data.txt
The password is IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR
```
#### Password
IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR


### level 11 → level 12
The password for the next level is stored in the file **data.txt**, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions.

#### Solution
  
  [Caesar cipher](https://gist.github.com/IQAndreas/030b8e91a8d9a407caa6)
  After logging into bandit11,
```sh=
bandit11@bandit:~$ strings data.txt | tr '[A-Za-z]' '[N-ZA-Mn-za-m]'
The password is 5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu
```
#### Password
5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu


### level 12 → level 13
The password for the next level is stored in the file **data.txt**, which is a hexdump of a file that has been repeatedly compressed. For this level it may be useful to create a directory under /tmp in which you can work using mkdir. For example: mkdir /tmp/myname123. Then copy the datafile using cp, and rename it using mv (read the manpages!)

- Tips 
    - Use `file` to get the information of files

- Takeaway
    - decompress gzip `gzip -d rdata.gz`
    - decompress bzip2 `bzip2 -d rdata.bz2`
    - decompress tar `tar -xvf rdata.tar`

#### Solution
  
  After logging into bandit12,
```sh=
bandit12@bandit:~$ mkdir /tmp/foo
bandit12@bandit:~$ cp data.txt /tmp/foo/data.txt
bandit12@bandit:~$ cd /tmp/foo
bandit12@bandit:/tmp/foo$ xxd -r data.txt rdata
bandit12@bandit:/tmp/foo$ strings rdata
data2.bin
BZh91AY&SY
{RBp
5(3A
7{qP
3A4$
q       \)
BB9<s
```

Use `file` to check it. Decompress the gzip file.
```sh=
bandit12@bandit:/tmp/foo$ file rdata
rdata: gzip compressed data, was "data2.bin", last modified: Thu May  7 18:14:30 2020, max compression, from Unix
bandit12@bandit:/tmp/foo$ mv rdata rdata.gz
bandit12@bandit:/tmp/foo$ gzip -d rdata.gz
bandit12@bandit:/tmp/foo$ ls
data.txt  rdata
bandit12@bandit:/tmp/foo$ file rdata
rdata: bzip2 compressed data, block size = 900k
```
Decompress the bzip2 file.
```sh=
bandit12@bandit:/tmp/foo$ mv rdata rdata.bz2
bandit12@bandit:/tmp/foo$ bzip2 -d rdata.bz2
bandit12@bandit:/tmp/foo$ ls
data.txt  rdata
bandit12@bandit:/tmp/foo$ file rdata
rdata: gzip compressed data, was "data4.bin", last modified: Thu May  7 18:14:30 2020, max compression, from Unix
bandit12@bandit:/tmp/foo$ mv rdata rdata.gz
bandit12@bandit:/tmp/foo$ gzip -d rdata.gz
```
Decompress the POSIX tar archive.
```sh=
bandit12@bandit:/tmp/foo$ file rdata
rdata: POSIX tar archive (GNU)
bandit12@bandit:/tmp/foo$ mv rdata rdata.tar
bandit12@bandit:/tmp/foo$ tar -xvf rdata.tar
data5.bin
```
MISC
```sh=
bandit12@bandit:/tmp/foo$ file data5.bin
data5.bin: POSIX tar archive (GNU)
bandit12@bandit:/tmp/foo$ mv data5.bin data.tar
bandit12@bandit:/tmp/foo$ tar -xvf data.tar
data6.bin
bandit12@bandit:/tmp/foo$ file data6.bin
data6.bin: bzip2 compressed data, block size = 900k
bandit12@bandit:/tmp/foo$ mv data6.bin rdata.bz2
bandit12@bandit:/tmp/foo$ bzip2 -d rdata.bz2
bandit12@bandit:/tmp/foo$ ls
data.tar  data.txt  rdata  rdata.tar
bandit12@bandit:/tmp/foo$ file rdata
rdata: POSIX tar archive (GNU)
bandit12@bandit:/tmp/foo$ mv rdata rdata.tar
bandit12@bandit:/tmp/foo$ tar -xvf rdata.tar
data8.bin
bandit12@bandit:/tmp/foo$ file data8.bin
data8.bin: gzip compressed data, was "data9.bin", last modified: Thu May  7 18:14:30 2020, max compression, from Unix
bandit12@bandit:/tmp/foo$ mv data8.bin data9.gz
bandit12@bandit:/tmp/foo$ gzip -d data9.gz
bandit12@bandit:/tmp/foo$ ls
data9  data.tar  data.txt  rdata.tar
bandit12@bandit:/tmp/foo$ file data9
data9: ASCII text
bandit12@bandit:/tmp/foo$ strings data9
The password is 8ZjyCRiBWFYkneahHwxCv3wb2a1ORpYL
```
#### Password
8ZjyCRiBWFYkneahHwxCv3wb2a1ORpYL


### level 13 → level 14
The password for the next level is stored in **/etc/bandit_pass/bandit14 and can only be read by user bandit14.** For this level, you don’t get the next password, but you get a private [[SSH]] key that can be used to log into the next level. **Note: localhost** is a hostname that refers to the machine you are working on.

#### Solution
  
  After logging into bandit13,
```sh=
bandit13@bandit:~$ ls
sshkey.private
bandit13@bandit:~$ ssh -i sshkey.private bandit14@localhost
...
Are you sure you want to continue connecting (yes/no)? yes
bandit14@bandit:~$ strings /etc/bandit_pass/bandit14
4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e
```
#### Password
4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e


### level 14 → level 15
The password for the next level can be retrieved by submitting the password of the current level to **port 30000 on localhost**.

#### Solution
  
  After logging into bandit14,
```sh=
bandit14@bandit:~$ nc localhost 30000
4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e
Correct!
BfMYroe26WYalil77FoDi9qh59eK5xNr
```

#### Solution
BfMYroe26WYalil77FoDi9qh59eK5xNr


### level 15 → level 16
The password for the next level can be retrieved by submitting the password of the current level to **port 30001 on localhost** using SSL encryption.

**Helpful note: Getting “HEARTBEATING” and “Read R BLOCK”? Use -ign_eof and read the “CONNECTED COMMANDS” section in the manpage. Next to ‘R’ and ‘Q’, the ‘B’ command also works in this version of that command…**

#### Solution
  
  After logging into bandit15,
```sh=
bandit15@bandit:~$ openssl s_client -crlf -connect localhost:30001 -servername localhost
...
BfMYroe26WYalil77FoDi9qh59eK5xNr
Correct!
cluFn7wTiGryunymYOu4RcffSxQluehd
```
#### Password
cluFn7wTiGryunymYOu4RcffSxQluehd


### level 16 → level 17
The credentials for the next level can be retrieved by submitting the password of the current level to **a port on localhost in the range 31000 to 32000**. First find out which of these ports have a server listening on them. Then find out which of those speak SSL and which don’t. There is only 1 server that will give the next credentials, the others will simply send back to you whatever you send to it.

- Tips
    - `[[ssh]]` by key

#### Solution
  
  After logging into bandit16,
```sh=
bandit16@bandit:~$ nmap localhost -p 31000-32000

Starting Nmap 7.40 ( https://nmap.org ) at 2021-07-02 14:24 CEST
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00023s latency).
Not shown: 996 closed ports
PORT      STATE SERVICE
31046/tcp open  unknown
31518/tcp open  unknown
31691/tcp open  unknown
31790/tcp open  unknown
31960/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 0.10 seconds
bandit16@bandit:~$ nmap localhost -p 31046 -sV -T4

Starting Nmap 7.40 ( https://nmap.org ) at 2021-07-02 14:26 CEST
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00012s latency).
PORT      STATE SERVICE VERSION
31046/tcp open  echo

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 41.51 seconds
bandit16@bandit:~$ nmap localhost -p 31790 -sV -T5

Starting Nmap 7.40 ( https://nmap.org ) at 2021-07-02 14:30 CEST
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00015s latency).
PORT      STATE SERVICE     VERSION
31790/tcp open  ssl/unknown
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port31790-TCP:V=7.40%T=SSL%I=7%D=7/2%Time=60DF06EF%P=x86_64-pc-linux-gn
SF:u%r(GenericLines,31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20cur
SF:rent\x20password\n")%r(GetRequest,31,"Wrong!\x20Please\x20enter\x20the\
SF:x20correct\x20current\x20password\n")%r(HTTPOptions,31,"Wrong!\x20Pleas
SF:e\x20enter\x20the\x20correct\x20current\x20password\n")%r(RTSPRequest,3
SF:1,"Wrong!\x20Please\x20enter\x20the\x20correct\x20current\x20password\n
SF:")%r(Help,31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20current\x2
SF:0password\n")%r(SSLSessionReq,31,"Wrong!\x20Please\x20enter\x20the\x20c
SF:orrect\x20current\x20password\n")%r(TLSSessionReq,31,"Wrong!\x20Please\
SF:x20enter\x20the\x20correct\x20current\x20password\n")%r(Kerberos,31,"Wr
SF:ong!\x20Please\x20enter\x20the\x20correct\x20current\x20password\n")%r(
SF:FourOhFourRequest,31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20cu
SF:rrent\x20password\n")%r(LPDString,31,"Wrong!\x20Please\x20enter\x20the\
SF:x20correct\x20current\x20password\n")%r(LDAPSearchReq,31,"Wrong!\x20Ple
SF:ase\x20enter\x20the\x20correct\x20current\x20password\n")%r(SIPOptions,
SF:31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20current\x20password\
SF:n");

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 88.73 seconds

bandit16@bandit:~$ openssl s_client -crlf -connect localhost:31790
...
cluFn7wTiGryunymYOu4RcffSxQluehd
Correct!
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----

closed
```
Save the key to the file `key1.key`, and use it to go to the next level.
```
bandit16@bandit:~$ mkdir /tmp/foo
bandit16@bandit:~$ cd /tmp/foo
bandit16@bandit:/tmp/foo$ vim key1.key
bandit16@bandit:/tmp/foo$ chmod 600 key1.key
bandit16@bandit:/tmp/foo$ ssh -i key1.key bandit17@localhost

```
#### Password
  

### level 17 → level 18
There are 2 files in the homedirectory: **passwords.old** and **passwords.new**. The password for the next level is in **passwords.new** and is the only line that has been changed between **passwords.old and passwords.new**

**NOTE: if you have solved this level and see ‘Byebye!’ when trying to log into bandit18, this is related to the next level, bandit19**

#### Solution
  
  After logging into bandit17,
```sh=
bandit17@bandit:~$ diff passwords.old passwords.new
42c42
< w0Yfolrc5bwjS4qw5mq1nnQi6mF03bii
---
> kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd
```
#### Password
kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd


### level 18 → level 19
The password for the next level is stored in a file **readme** in the homedirectory. Unfortunately, someone has modified **.bashrc** to log you out when you log in with SSH.

- Tips
    - [[ssh]] with command

#### Solution
  
```sh=
$ ssh bandit.labs.overthewire.org -l bandit18 -p 2220 strings readme
This is a OverTheWire game server. More information on http://www.overthewire.org/wargames

bandit18@bandit.labs.overthewire.org's password:
IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x
```
#### Password
IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x


### level 19 → level 20
To gain access to the next level, you should use the setuid binary in the homedirectory. Execute it without arguments to find out how to use it. The password for this level can be found in the usual place (/etc/bandit_pass), after you have used the setuid binary.

#### Solution
  
```sh=
bandit19@bandit:~$ ./bandit20-do strings /etc/bandit_pass/bandit20
GbKksEFF4yrVs6il55v6gwY5aVje5f0j
```
#### Password
GbKksEFF4yrVs6il55v6gwY5aVje5f0j

### level 20 → level 21
There is a setuid binary in the homedirectory that does the following: it makes a connection to localhost on the port you specify as a commandline argument. It then reads a line of text from the connection and compares it to the password in the previous level (bandit20). If the password is correct, it will transmit the password for the next level (bandit21).

**NOTE**: Try connecting to your own network daemon to see if it works as you think.

- Tips
    - [create listener](https://unix.stackexchange.com/questions/214471/how-to-create-a-tcp-listener)
    - tmux

#### Solution

Use `tmux` to create two sessions.
Pane 1
```sh=
bandit20@bandit:~$ nc -l -p 8787
GbKksEFF4yrVs6il55v6gwY5aVje5f0j
gE269g2h3mw3pwgrj0Ha9Uoqen1c9DGr
```
Pane 2
```sh=
bandit20@bandit:~$ ./suconnect 8787
Read: GbKksEFF4yrVs6il55v6gwY5aVje5f0j
Password matches, sending next password
```
#### Solution
gE269g2h3mw3pwgrj0Ha9Uoqen1c9DGr

### level 21 → level 22
A program is running automatically at regular intervals from **cron**, the time-based job scheduler. Look in **/etc/cron.d/** for the configuration and see what command is being executed.

#### Solution
  
```sh=
bandit21@bandit:~$ ls /etc/cron.d
cronjob_bandit15_root  cronjob_bandit17_root  cronjob_bandit22  cronjob_bandit23  cronjob_bandit24  cronjob_bandit25_root
bandit21@bandit:~$ strings /etc/cron.d/cronjob_bandit22
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
bandit21@bandit:~$ strings /usr/bin/cronjob_bandit22.sh
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
bandit21@bandit:~$ cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
Yk7owGAcWjwMVRwrTesJEwB7WVOiILLI
```
#### Password
Yk7owGAcWjwMVRwrTesJEwB7WVOiILLI

### level 22 → level 23
A program is running automatically at regular intervals from **cron**, the time-based job scheduler. Look in **/etc/cron.d/** for the configuration and see what command is being executed.

**NOTE:** Looking at shell scripts written by other people is a very useful skill. The script for this level is intentionally made easy to read. If you are having problems understanding what it does, try executing it to see the debug information it prints.

#### Solution
  
```sh=
bandit22@bandit:~$ ls /etc/cron.d
cronjob_bandit15_root  cronjob_bandit17_root  cronjob_bandit22  cronjob_bandit23  cronjob_bandit24  cronjob_bandit25_root
bandit22@bandit:~$ strings /etc/cron.d/cronjob_bandit23
@reboot bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
bandit22@bandit:~$ strings /usr/bin/cronjob_bandit23.sh
#!/bin/bash
myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)
echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"
cat /etc/bandit_pass/$myname > /tmp/$mytarget
bandit22@bandit:~$ echo I am user bandit23 | md5sum | cut -d ' ' -f 1
8ca319486bfbbc3663ea0fbe81326349
bandit22@bandit:~$ strings /tmp/8ca319486bfbbc3663ea0fbe81326349
jc1udXuA1tiHqjIsL8yaapX5XIAI6i0n
```
#### Password
jc1udXuA1tiHqjIsL8yaapX5XIAI6i0n

### level 23 → level 24
A program is running automatically at regular intervals from **cron**, the time-based job scheduler. Look in **/etc/cron.d/** for the configuration and see what command is being executed.

**NOTE:** This level requires you to create your own first shell-script. This is a very big step and you should be proud of yourself when you beat this level!

**NOTE 2:** Keep in mind that your shell script is removed once executed, so you may want to keep a copy around…

- Hint
    - passwords are stored in `/etc/bandit_pass`

- Tips
    - `chmod`
    - `cp`
    - shell script

#### Solution
  
```sh=
bandit23@bandit:~$ strings /usr/bin/cronjob_bandit24.sh
#!/bin/bash
myname=$(whoami)
cd /var/spool/$myname
echo "Executing and deleting all scripts in /var/spool/$myname:"
for i in * .*;
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" ./$i)"
        if [ "${owner}" = "bandit23" ]; then
            timeout -s 9 60 ./$i
        fi
        rm -f ./$i
    fi
done
bandit23@bandit:~$ cd /tmp
bandit23@bandit:/tmp$ mkdir ss
bandit23@bandit:/tmp$ cd ss
bandit23@bandit:/tmp/ss$ vim
bandit23@bandit:/tmp/ss$ ls
gogo.sh
bandit23@bandit:/tmp/ss$ chmod 777 gogo.sh
bandit23@bandit:/tmp/ss$ chmod 777 /tmp/ss
bandit23@bandit:/tmp/ss$ strings gogo.sh
#!/bin/bash
strings /etc/bandit_pass/bandit24 > /tmp/ss/b24.txt
bandit23@bandit:/tmp/ss$ cp gogo.sh /var/spool/bandit24/
```
Wait few seconds.
```
bandit23@bandit:/tmp/ss$ ls
b24.txt  gogo.sh
bandit23@bandit:/tmp/ss$ strings b24.txt
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ
```
#### Password
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ

### level 24 → level 25
A daemon is listening on port 30002 and will give you the password for bandit25 if given the password for bandit24 and a secret numeric 4-digit pincode. There is no way to retrieve the pincode except by going through all of the 10000 combinations, called brute-forcing.

#### Solution
  
```sh=
bandit22@bandit:~$ cd /tmp
bandit22@bandit:~$ mkdir ss
bandit22@bandit:~$ cd ss
bandit22@bandit:~$ vim gogo.sh
bandit24@bandit:/tmp/ss$ strings gogo.sh
#!/bin/bash
for i in $(seq 0 9999); do
    if [ "${#i}" = "1" ]; then
        echo UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 000$i
    elif [ "${#i}" = "2" ]; then
        echo UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 00$i
    elif [ "${#i}" = "3" ]; then
        echo UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ 0$i
    elif [ "${#i}" = "4" ]; then
        echo UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ $i
    fi
done
bandit24@bandit:/tmp/ss$ sh gogo.sh > tt.txt
bandit24@bandit:/tmp/ss$ strings tt.txt | nc localhost 30002
Wrong! Please enter the correct pincode. Try again.
Wrong! Please enter the correct pincode. Try again.
...
Wrong! Please enter the correct pincode. Try again.
Correct!
The password of user bandit25 is uNG9O58gUE7snukf3bvZ0rxhtnjzSGzG

Exiting.
```
#### Password
uNG9O58gUE7snukf3bvZ0rxhtnjzSGzG

### level 25 → level 26
Logging in to bandit26 from bandit25 should be fairly easy… The shell for user bandit26 is not **/bin/bash**, but something else. Find out what it is, how it works and how to break out of it.

- Tips
    - how to view system users
    - knowledge to more
    - vim

#### Solution
  
```sh=
bandit25@bandit:~$ ls
bandit26.sshkey
bandit25@bandit:~$ ssh -i bandit26.sshkey bandit26@localhost
...

  _                     _ _ _   ___   __
 | |                   | (_) | |__ \ / /
 | |__   __ _ _ __   __| |_| |_   ) / /_
 | '_ \ / _` | '_ \ / _` | | __| / / '_ \
 | |_) | (_| | | | | (_| | | |_ / /| (_) |
 |_.__/ \__,_|_| |_|\__,_|_|\__|____\___/
Connection to localhost closed.
```
[How To View System Users in Linux on Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-view-system-users-in-linux-on-ubuntu)
check the `/etc/passwd`
```
bandit25@bandit:~$ strings /etc/passwd | grep bandit26
bandit26:x:11026:11026:bandit level 26:/home/bandit26:/usr/bin/showtext
```
Therefore, we know bandit26 use `usr/bin/showtext`.
```
bandit25@bandit:/usr/bin$ strings showtext
#!/bin/sh
export TERM=linux
more ~/text.txt
exit 0
```
The `showtext` run the more command and log out. The only way to get the password is get the content of `etc/bandit_pass/bandit26` before `exit 0 ` executed. It seems that the `text.txt` is 
```

  _                     _ _ _   ___   __
 | |                   | (_) | |__ \ / /
 | |__   __ _ _ __   __| |_| |_   ) / /_
 | '_ \ / _` | '_ \ / _` | | __| / / '_ \
 | |_) | (_| | | | | (_| | | |_ / /| (_) |
 |_.__/ \__,_|_| |_|\__,_|_|\__|____\___/
```
The command `more` display the file page by page. Resize the commandline window to prevent `more` from completing. Then we can use `v` to edit `text.txt`. In the vim, use the `:e` command to open the file containing password.
```
5czgV9L3Xx8JPOyRbXh6lQbmIOWvPT6Z
```
### Password
5czgV9L3Xx8JPOyRbXh6lQbmIOWvPT6Z

### level 26 → level 27
Good job getting a shell! Now hurry and grab the password for bandit27!

#### Solution
  
```sh=
bandit22@bandit:~$ ls /etc/cron.d
cronjob_bandit15_root  cronjob_bandit17_root  cronjob_bandit22  cronjob_bandit23  cronjob_bandit24  cronjob_bandit25_root
bandit22@bandit:~$ strings /etc/cron.d/cronjob_bandit23
@reboot bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
bandit22@bandit:~$ strings /usr/bin/cronjob_bandit23.sh
#!/bin/bash
myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)
echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"
cat /etc/bandit_pass/$myname > /tmp/$mytarget
bandit22@bandit:~$ echo I am user bandit23 | md5sum | cut -d ' ' -f 1
8ca319486bfbbc3663ea0fbe81326349
bandit22@bandit:~$ strings /tmp/8ca319486bfbbc3663ea0fbe81326349
jc1udXuA1tiHqjIsL8yaapX5XIAI6i0n
```
#### Password
3ba3118a22e93127a4ed485be72ef5ea

### level 27 → level 28
There is a git repository at `ssh://bandit27-git@localhost/home/bandit27-git/repo`. The password for the user `bandit27-git` is the same as for the user `bandit27`.

Clone the repository and find the password for the next level.
#### Solution
  
```
bandit27@bandit:~$ mkdir /tmp/ssss
bandit27@bandit:~$ cd /tmp/ssss
bandit27@bandit:/tmp/ssss$ git clone ssh://bandit27-git@localhost/home/bandit27-git/repo
bandit27-git@localhost's password:
remote: Counting objects: 3, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 0 (delta 0)
Receiving objects: 100% (3/3), done.
bandit27@bandit:/tmp/ssss$ ls
repo
bandit27@bandit:/tmp/ssss$ cd repo
bandit27@bandit:/tmp/ssss/repo$ ls
README
bandit27@bandit:/tmp/ssss/repo$ strings README
The password to the next level is: 0ef186ac70e04ea33b4c1853d2526fa2
```
#### Password
0ef186ac70e04ea33b4c1853d2526fa2

### level 28 → level 29
There is a git repository at `ssh://bandit28-git@localhost/home/bandit28-git/repo`. The password for the user `bandit28-git` is the same as for the user `bandit28`.

Clone the repository and find the password for the next level.
#### Solution
  
```
bandit28@bandit:~$ mkdir /tmp/oso
bandit28@bandit:~$ cd /tmp/oso
bandit28@bandit:/tmp/oso$ ls
bandit28@bandit:/tmp/oso$ git clone ssh://bandit28-git@localhost/home/bandit28-git/repo
bandit28@bandit:/tmp/oso$ ls
repo
bandit28@bandit:/tmp/oso$ cd repo
bandit28@bandit:/tmp/oso/repo$ ls
README.md
bandit28@bandit:/tmp/oso/repo$ strings README.md
# Bandit Notes
Some notes for level29 of bandit.
## credentials
- username: bandit29
- password: xxxxxxxxxx
bandit28@bandit:/tmp/oso/repo$ git log
commit edd935d60906b33f0619605abd1689808ccdd5ee
Author: Morla Porla <morla@overthewire.org>
Date:   Thu May 7 20:14:49 2020 +0200

    fix info leak

commit c086d11a00c0648d095d04c089786efef5e01264
Author: Morla Porla <morla@overthewire.org>
Date:   Thu May 7 20:14:49 2020 +0200

    add missing data

commit de2ebe2d5fd1598cd547f4d56247e053be3fdc38
Author: Ben Dover <noone@overthewire.org>
Date:   Thu May 7 20:14:49 2020 +0200

    initial commit of README.md
bandit28@bandit:/tmp/oso/repo$ git checkout c086d11a00c0648d095d04c089786efef5e01264
Note: checking out 'c086d11a00c0648d095d04c089786efef5e01264'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at c086d11... add missing data
bandit28@bandit:/tmp/oso/repo$ ls
README.md
bandit28@bandit:/tmp/oso/repo$ strings README.md
# Bandit Notes
Some notes for level29 of bandit.
## credentials
- username: bandit29
- password: bbc96594b4e001778eee9975372716b2
```
#### Password
bbc96594b4e001778eee9975372716b2

### level 29 → level 30
There is a git repository at `ssh://bandit29-git@localhost/home/bandit29-git/repo`. The password for the user `bandit29-git` is the same as for the user `bandit29`.

Clone the repository and find the password for the next level.
#### Solution
  
```
bandit29@bandit:~$ mkdir /tmp/s29
bandit29@bandit:~$ cd /tmp/s29
bandit29@bandit:/tmp/s29$ git clone ssh://bandit29-git@localhost/home/bandit29-git/repo
bandit29@bandit:/tmp/s29$ cd repo
bandit29@bandit:/tmp/s29/repo$ ls
README.md
bandit29@bandit:/tmp/s29/repo$ strings README.md
# Bandit Notes
Some notes for bandit30 of bandit.
## credentials
- username: bandit30
- password: <no passwords in production!>
bandit29@bandit:/tmp/s29/repo$ git branch -a
* (HEAD detached at 18a6fd6)
  master
  remotes/origin/HEAD -> origin/master
  remotes/origin/dev
  remotes/origin/master
  remotes/origin/sploits-dev
bandit29@bandit:/tmp/s29/repo$ git checkout dev
bandit29@bandit:/tmp/s29/repo$ strings README.md
# Bandit Notes
Some notes for bandit30 of bandit.
## credentials
- username: bandit30
- password: 5b90576bedb2cc04c86a9e924ce42faf
```
#### Password
5b90576bedb2cc04c86a9e924ce42faf

### level 30 → level 31
There is a git repository at `ssh://bandit30-git@localhost/home/bandit30-git/repo`. The password for the user `bandit30-git` is the same as for the user `bandit30`.

Clone the repository and find the password for the next level.

- Problem
    - git tag without any commit (how?)

#### Solution
  
```
bandit30@bandit:~$ mkdir /tmp/s30
bandit30@bandit:~$ cd /tmp/s30
bandit30@bandit:/tmp/s30$ git clone ssh://bandit30-git@localhost/home/bandit30-git/repo
bandit30@bandit:/tmp/s30$ cd repo
bandit30@bandit:/tmp/s30/repo$ ls
README.md
bandit30@bandit:/tmp/s30/repo$ strings README.md
just an epmty file... muahaha
bandit30@bandit:/tmp/s30/repo$ git branch -a
\* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/master
bandit30@bandit:/tmp/s30/repo$ git log
commit 3aefa229469b7ba1cc08203e5d8fa299354c496b
Author: Ben Dover <noone@overthewire.org>
Date:   Thu May 7 20:14:54 2020 +0200

    initial commit of README.md
bandit30@bandit:/tmp/s30/repo$ git tag
secret
bandit30@bandit:/tmp/s30/repo$ git show secret
47e603bb428404d265f59c42920d81e5
```
#### Password
47e603bb428404d265f59c42920d81e5

### level 31 → level 32
There is a git repository at `ssh://bandit31-git@localhost/home/bandit31-git/repo`. The password for the user `bandit31-git` is the same as for the user `bandit31`.

Clone the repository and find the password for the next level.

#### Solution
  
```
bandit31@bandit:~$ mkdir /tmp/s31
bandit31@bandit:~$ cd /tmp/s31
bandit31@bandit:/tmp/s31$ git clone ssh://bandit31-git@localhost/home/bandit31-git/repo
bandit31@bandit:/tmp/s31$ cd repo
bandit31@bandit:/tmp/s31/repo$ ls
README.md
bandit31@bandit:/tmp/s31/repo$ strings README.md
This time your task is to push a file to the remote repository.
Details:
    File name: key.txt
    Content: 'May I come in?'
    Branch: master
bandit31@bandit:/tmp/s31/repo$ echo 'May I come in?' > key.txt
bandit31@bandit:/tmp/s31/repo$ vim .gitignore
bandit31@bandit:/tmp/s31/repo$ git status
bandit31@bandit:/tmp/s31/repo$ git add .
bandit31@bandit:/tmp/s31/repo$ git commit -m "add key.txt"
[master 07562e9] add key.txt
 2 files changed, 1 insertion(+), 1 deletion(-)
 create mode 100644 key.txt
bandit31@bandit:/tmp/s31/repo$ git push
Could not create directory '/home/bandit31/.ssh'.
The authenticity of host 'localhost (127.0.0.1)' can't be established.
ECDSA key fingerprint is SHA256:98UL0ZWr85496EtCRkKlo20X3OPnyPSB5tB5RPbhczc.
Are you sure you want to continue connecting (yes/no)? yes
Failed to add the host to the list of known hosts (/home/bandit31/.ssh/known_hosts).
This is a OverTheWire game server. More information on http://www.overthewire.org/wargames

bandit31-git@localhost's password:
Counting objects: 4, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (4/4), 333 bytes | 0 bytes/s, done.
Total 4 (delta 0), reused 0 (delta 0)
remote: ### Attempting to validate files... ####
remote:
remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
remote:
remote: Well done! Here is the password for the next level:
remote: 56a9bf19c63d650ce78e6ec0354ee45e
remote:
remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
remote:
To ssh://localhost/home/bandit31-git/repo
 ! [remote rejected] master -> master (pre-receive hook declined)
error: failed to push some refs to 'ssh://bandit31-git@localhost/home/bandit31-git/repo'
```
#### Password
56a9bf19c63d650ce78e6ec0354ee45e

### level 32 → level 33
After all this `git` stuff its time for another escape. Good luck!

- Tips
    - [$0](https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#Positional-Parameters)


#### Solution
  
```
WELCOME TO THE UPPERCASE SHELL
>> ls
sh: 1: LS: not found
>> echo
sh: 1: ECHO: not found
>> $shell
WELCOME TO THE UPPERCASE SHELL
>> $0
$ whoami
bandit33
$ strings /etc/bandit_pass/bandit33
c9c3199ddf4121b10cf581a98d51caee
```
#### Password
c9c3199ddf4121b10cf581a98d51caee


### level 33 → level 34
**At this moment, level 34 does not exist yet.**

#### Solution
  
```
bandit33@bandit:~$ ls
README.txt
bandit33@bandit:~$ strings README.txt
Congratulations on solving the last level of this game!
At this moment, there are no more levels to play in this game. However, we are constantly working
on new levels and will most likely expand this game with more levels soon.
Keep an eye out for an announcement on our usual communication channels!
In the meantime, you could play some of our other wargames.
If you have an idea for an awesome new level, please let us know!
```
#### Password
c9c3199ddf4121b10cf581a98d51caee