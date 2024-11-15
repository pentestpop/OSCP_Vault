### creating ssh key
- ssh-keygen
- `ssh -p 2222(unless 22) -i $created_key(no pub) $user@$host`
- Using a id_sa (private key) from /home/user/.ssh/id_sa

### Password Protected SSH key
1. may need to chmod 600 id_rsa (too many permissions won't work)
2. ssh2john id_rsa > ssh.hash
3. remove "id_rsa:" from ssh.hash
4. hashcat -h | grep -i "ssh" (22921 for example)
5. hashcat -m 22921 ssh.hash ssh.passwords -r ssh.rule --force