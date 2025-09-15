---
title: natas 11 walkthrough
description: over the wire natas 11
layout: libdoc_page
date: 1111-11-11
eleventyNavigation:
    key: natas-11 OverTheWire CTFs
    parent: OverTheWire CTFs
    title: natas 11 walkthrough
    order: 30
---
## Description
>   Username: natas11
> 
>   URL:      http://natas11.natas.labs.overthewire.org
### Source Code
```php
<?

$defaultdata = array( "showpassword"=>"no", "bgcolor"=>"#ffffff");

function xor_encrypt($in) {
    $key = '<censored>';
    $text = $in;
    $outText = '';

    // Iterate through each character
    for($i=0;$i<strlen($text);$i++) {
    $outText .= $text[$i] ^ $key[$i % strlen($key)];
    }

    return $outText;
}

function loadData($def) {
    global $_COOKIE;
    $mydata = $def;
    if(array_key_exists("data", $_COOKIE)) {
    $tempdata = json_decode(xor_encrypt(base64_decode($_COOKIE["data"])), true);
    if(is_array($tempdata) && array_key_exists("showpassword", $tempdata) && array_key_exists("bgcolor", $tempdata)) {
        if (preg_match('/^#(?:[a-f\d]{6})$/i', $tempdata['bgcolor'])) {
        $mydata['showpassword'] = $tempdata['showpassword'];
        $mydata['bgcolor'] = $tempdata['bgcolor'];
        }
    }
    }
    return $mydata;
}

function saveData($d) {
    setcookie("data", base64_encode(xor_encrypt(json_encode($d))));
}

$data = loadData($defaultdata);

if(array_key_exists("bgcolor",$_REQUEST)) {
    if (preg_match('/^#(?:[a-f\d]{6})$/i', $_REQUEST['bgcolor'])) {
        $data['bgcolor'] = $_REQUEST['bgcolor'];
    }
}

saveData($data);



?>

<h1>natas11</h1>
<div id="content">
<body style="background: <?=$data['bgcolor']?>;">
Cookies are protected with XOR encryption<br/><br/>

<?
if($data["showpassword"] == "yes") {
    print "The password for natas12 is <censored><br>";
}

?>
```

### Cookie
Name : data 
Value : HmYkBwozJw4WNyAAFyB1VUcqOE1JZjUIBis7ABdmbU1GIjEJAyIxTRg=
Domain : natas11.natas.labs.overthewire.org


---
## Progress
### Observation
default data --(json encode, `xor_encrypt`, base64 encode)--> cookie

Therefore, we know that `json_encoded(data) xor key = base64_decode(cookie)`.
From the property of xor $a \oplus b = c \leftrightarrow a \oplus c = b$, we can get the key by  `key = json_encoded(data) xor base64_decode(cookie)`.
### Get the Key
Use the following code to get the key.
```php
$defaultdata = array( "showpassword"=>"no", "bgcolor"=>"#ffffff");
$natasCookie = "HmYkBwozJw4WNyAAFyB1VUcqOE1JZjUIBis7ABdmbU1GIjEJAyIxTRg=";
function xors($a, $b) {
	$key = $a;
    $text = $b;
    $outText = '';

    // Iterate through each character
    for($i=0;$i<strlen($text);$i++) {
    $outText .= $text[$i] ^ $key[$i % strlen($key)];
    }

    return $outText;
}
$natasKey = xors(base64_decode($natasCookie),json_encode($defaultdata));
echo $natasKey;
```
Key = eDWoeDWoeDWoeDWoeDWoeDWoeDWoeDWoeDWoeDWoe
### Make Payload
The payload should contain two keys, `showpassword` and `bgcolor`.
```php
$payload = array( "showpassword"=>"yes", "bgcolor"=>"#fffaaf");
$myCookie = base64_encode(xor_encrypt(json_encode($payload)));
echo "my cookie payload : ";
echo $myCookie;
echo "\n";
```

### Get the Password
When using key = eDWoeDWoeDWoeDWoeDWoeDWoeDWoeDWoeDWoeDWoe, it failed.
Guess the key should be `eDWo`. Then, it works.

---
## Hint

---
## Solving


### Techniques
- [[xor]]

### Tools
- 