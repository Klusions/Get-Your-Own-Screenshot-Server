# Own-Screenshot-Server #
# Create your own Screenshot-Server #

> This is a Tutorial about ShareX ( https://getsharex.com/ )
> If you don't want to own a personal Server you can use prntscr or imgur

---

**Require**

- Apache 2 Webserver
- Share X
- PHP

---

**After installing Share X**
> Open "upload.php" & change "yourdomain.com" to your domain. 
> Edit "yourkey" to a password.

---

## upload.php file ##

```php
<?php
$key            = "yourkey";
$redirect       = "http://yourdomain.com/";
$domain         = "yourdomain.com";
$filenamelength = 8;
#Comment this next line if you want Robots to index this site.
if ($_SERVER["REQUEST_URI"] == "/robot.txt") {
    die("User-agent: *\nDisallow: /");
}
#Don't edit below this line inless you know what you are doing...
$urldata = explode("/", $_SERVER["REQUEST_URI"]);

if (isset($_POST['k'])) {
    if ($_POST['k'] != $key) {
        echo ('Error Key wrong.');
        die();
    }
}

function createthumb($name, $filename, $new_w, $new_h)
{
    $system  = explode('.', $name);
    $src_img = imagecreatefromstring(file_get_contents($name));
    $old_x   = imageSX($src_img);
    $old_y   = imageSY($src_img);
    if ($old_x > $old_y) {
        $thumb_w = $new_w;
        $thumb_h = $old_y * ($new_h / $old_x);
    }
    if ($old_x < $old_y) {
        $thumb_w = $old_x * ($new_w / $old_y);
        $thumb_h = $new_h;
    }
    if ($old_x == $old_y) {
        $thumb_w = $new_w;
        $thumb_h = $new_h;
    }
    $dst_img = ImageCreateTrueColor($thumb_w, $thumb_h);
    imagecopyresampled($dst_img, $src_img, 0, 0, 0, 0, $thumb_w, $thumb_h, $old_x, $old_y);
    if (preg_match("/png/", $system[1])) {
        imagepng($dst_img, $filename);
    } else {
        imagejpeg($dst_img, $filename);
    }
    imagedestroy($dst_img);
    imagedestroy($src_img);
}
if (isset($_GET['tn'])) {
    if (!file_exists('tn_' . $_GET['tn'])) {
        createthumb($_GET['tn'], 'tn_' . $_GET['tn'], 100, 100);
    }
    header('Location: http://' . $domain . '/tn_' . $_GET['tn']);
    die();
}

if (isset($_GET['k'])) {
    if ($_GET['k'] == $key) {
        if (isset($_GET['delete'])) {
            if (file_exists($_GET['delete'])) {
                if (isset($_GET['delete'])) {
                    if (file_exists('tn_' . $_GET['delete'])) {
                        unlink('tn_' . $_GET['delete']);
                    }
                    unlink($_GET['delete']);
                    echo "Your uploaded file has been deleted";
                    die();
                } else {
                    echo "Your file you want deleted does not exist";
                }
            }
        }
    }
}
if (isset($_POST['k'])) {
    if ($_POST['k'] == $key) {
        header("Content-Type: application/json");
        if (isset($_GET['up'])) {
            $target = getcwd() . "/" . basename($_FILES['d']['name']);
            if (move_uploaded_file($_FILES['d']['tmp_name'], $target)) {
                $filename = substr(str_shuffle("abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"), 0, $filenamelength) . "." . end(explode(".", $_FILES["d"]["name"]));
                while (file_exists($filename)) {
                    $filename = substr(str_shuffle("abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"), 0, $filenamelength) . "." . end(explode(".", $_FILES["d"]["name"]));
                }
                rename(getcwd() . "/" . basename($_FILES['d']['name']), getcwd() . "/" . $filename);
                echo json_encode(array(
                    'filename' => $filename,
                    'key' => $key
                ));
            } else {
                echo "Sorry, there was a problem uploading your file.";
            }
        }
    } else {
        echo ('404');
    }
} else {
    echo ('Welcome.');
}
?>
```

## .htaccess file ##
```php
<Files *.php>
    php_flag engine off
    AddType text/plain php
</Files>

<Files index.php>
    Order Allow,Deny
    Allow from all
    php_flag engine on
    RemoveType text/plain php
</Files>
```
---

> Upload both files to your FTP and give them full permissions (777)
Go to ShareX >Destinations >Destination Settings >Scroll down to custom uploaders - Click on add and change everything to this ->[/COLOR]

[![Screen-1](https://i.imgur.com/Z7miloA.png)]()

> http://yourdomain.com/upload.php/?up=
- \"domain\":\"(.+?)\"
- \"key\":\"(.+?)\"
- \"filename\":\"(.+?)\"

> http://yourdomain.com/$json:filename$
> http://yourdomain.com/?tn=$json:filename$
> http://yourdomain.com/?k=$json:key$&delete=$json:filename$

> Click on Update to save it. (At the left site, not in the middle)
Change your Destination settings to your new Screenshot-Server is done!
[![Screen-1](https://i.imgur.com/LDnCGIb.png)]()

If you have any questions, hit me up!
