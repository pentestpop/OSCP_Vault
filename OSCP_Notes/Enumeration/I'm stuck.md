Remember: Enumerate deeply, exploit simply.

Did you do all of these?
- `sudo nmap -v -p- -sC -sV 192.168.100.101`
- `sudo nmap -sU 192.168.100.101`
- `nxc smb 192.168.101.100 -u '' -p '' --shares`
- `nxc smb 192.168.101.100 -u '' -p '' --users`
- `nxc ldap 192.168.101.100 -u '' -p '' -M get-desc-users`
- `nxc ldap 192.168.101.100 -u '' -p '' --password-not-required --admin-count --users --groups`
- `enum4linux -a $IP`

### Web Server
Did you fuzz for extensions "--extensions php,rb,txt" in feroxbuster?
Did you check for subdomains too, not just subdirectories?
Did you add your domain name to the `/etc/hosts` file?
If you see a real blog on a lab (as opposed to Lorem Ipsum), read it

### Other Tips
Use `nc` to connect directly with a port to see if you can get any output.  This can grab banners. 

Upload a file to SMB/FTP server to try and execute from the web server

Did you try to use different ports? Specifically the ports the target has open for reverse shells?

Did you try to use `domain.com/user` or just `user`?
Same with `local-auth`

Try `crackstation` or `NTLM.pw`

### Strategy
When facing a Windows server with so many ports, I’ll typically start working them prioritized by my comfort level. I’ll generate a tiered list, with some rough ideas of what I might look for on each:

- Must Look AT
    - SMB - Look for any open shares and see what I might find there.
    - LDAP - Can I get any information without credentials?
- If those fail
    - Kerberos - Can I brute force usernames? If I find any, are they AS-REP-Roast-able?
    - DNS - Can I do a zone transfer? Brute force any subdomains?
    - RPC - Is anonymous access possible?
- Note for creds
    - WinRM - If I can find creds for a user in the Remote Management Users group, I can get a shell