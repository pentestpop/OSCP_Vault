**1. ADD THE DNS NAME TO YOUR `/etc/hosts` FILE**
- `dc.domain.com` AND domain.com`

To enumerate accounts ON DC:
`kerbrute userenum --dc $ip -d CONTROLLER.local Users.txt`
- --dc can point to a domain 
- probably `kerbrute_linux_arm userenum -d $domain.com --dc $IP users.txt`

To check for users on 445 with RPC:
`rpcclient -U "" -N $IP`
	- `enumdomusers`
	- `querygroup 0x200`  
	- `querygroupmem 0x200`
	- `queryuser 0x1f4` 


### Other AD Enum
`enum4linux -u "" -p "" -a <DC IP> && enum4linux -u "guest" -p ""-a <DC IP>`

`smbmap -u "" -p "" -P 445 -H <DC IP> && smbmap -u "guest" -p "" -P 445 -H <DC IP>`
`smbclient -U '%' -L //<DC IP> && smbclient -U 'guest%' -L //`
`nmap -n -sV --script "ldap* and not brute" -p 389 <DC IP>`

### Suggestions
- ASRepRoast if username but no password