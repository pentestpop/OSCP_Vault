`ssh -i $key $user@$target`

alternate port `ssh -p $port $user@$target`

You can connect to the ssh service via netcat to grab the banner and search the version for OS info.
- `nc -nv $IP 22`

### Brute forcing:
With no creds:
- `hydra -L usernames.txt -P passwords.txt 192.168.100.101 ssh`

With a username:
- `hydra -l $user -P passwords.txt 192.168.100.101 ssh`

With a passwords:
- ` hydra -L usernames.txt -p $password 192.168.100.101 ssh`

Useful nmap scripts:
- ssl-heartbleed.nse

SSH permissions too open?
`chmod + 600 $key.id_rsa`