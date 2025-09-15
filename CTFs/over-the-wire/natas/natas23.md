---
title: natas 23 walkthrough
description: over the wire natas 23
layout: libdoc_page
date: 1111-11-11
eleventyNavigation:
    key: natas-23 OverTheWire CTFs
    parent: OverTheWire CTFs
    title: natas 23 walkthrough
    order: 30
---
## Description
> Username: natas23
> 
> URL:      http://natas23.natas.labs.overthewire.org

### Source Code
```php
<?php
if (array_key_exists("passwd", $_REQUEST)) {
    if (strstr($_REQUEST["passwd"], "iloveyou") && ($_REQUEST["passwd"] > 10)) {
        echo "<br>The credentials for the next level are:<br>";
        echo "<pre>Username: natas24 Password: <censored></pre>";
    } else {
        echo "<br>Wrong!<br>";
    }
}
// morla / 10111
```

---
## Progress
### Observation
I have no idea why the password is compared to a number, but the only thing we need to do is pas the check.
This [php wiki](https://wiki.php.net/rfc/string_to_number_comparison#precision) explained how to compare string to integer.
### Payload
`30iloveyou`
0xzF30T9Av8lgXhW7slhFCIsVKAPyl2r

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