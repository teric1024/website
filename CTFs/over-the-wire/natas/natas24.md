---
title: natas 24 walkthrough
description: over the wire natas 24
layout: libdoc_page
date: 1111-11-11
eleventyNavigation:
    key: natas-24 OverTheWire CTFs
    parent: OverTheWire CTFs
    title: natas 24 walkthrough
    order: 30
---
## Description
> Username: natas24
> 
> URL:      http://natas24.natas.labs.overthewire.org

### Source Code
```php
<?php
if (array_key_exists("passwd", $_REQUEST)) {
    if (!strcmp($_REQUEST["passwd"], "<censored>")) {
        echo "<br>The credentials for the next level are:<br>";
        echo "<pre>Username: natas25 Password: <censored></pre>";
    } else {
        echo "<br>Wrong!<br>";
    }
}
// morla / 10111
```

---
## Progress
### Observation
In [this document](https://www.php.net/manual/en/language.types.boolean.php), it says what kind of number would be treated as false in php. No wonder, 0 is the only number would be treated as false.
If the two inputs are equal, `strcmp` returns 0. Therefore, send passwd as `<censored>` would pass.
Wait. How could we send the password before we get it. The only way is to find a weakness in `strcmp`.
### Survey
I found [php strcmp bypass](https://www.doyler.net/security-not-included/bypassing-php-strcmp-abctf2016), and it refers [php wiki](https://www.php.net/manual/en/function.strcmp.php#121789).  They say when strcmp returns NULL on failure. Moreover, NULL is treated as false. 
Then, making the input be any data type that cannot be transferred into string automatically would be fine.
### Trial
Well, try the input given by those reference.
`http://natas24.natas.labs.overthewire.org/?passwd[]=""
Password: O9QD9DZBDq1YpswiTM5oqMDaOtuZtAcx

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