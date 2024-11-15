Commands:
- VRFY command tells if an email address exists.
- EXPN command shows membership of mailing list
- RCPT (you'll need a valid email for this for an exploit)

### smtp-user-enum
- To verify usernames: `smtp-user-enum -M VRFY -U users.txt -t $host` 
	- host is IP or hostname
- `smtp-user-enum -M EXPN -u $username -t $host`
- `smtp-user-enum -M RCPT -U users.txt -T $hostlist`
- `smtp-user-enum -M EXPN -D $domain -U users.txt -t $host`

### Swaks
Swaks (Sending email from command line when you have creds for mail server)
- `swaks --to <recipient@email.com> --from <sender@email.com> -ap --attach @<attachment> --server <mail server ip> --body "message" --header "Subject: Subject" --suppress-data`
	- You will need the password of the mail server user (likely the sender)
	- Note that the mail server may not be the same machine as the user who opens the email

### Send email over NC
pop@kali--[~/PG/Postfish]$
1. `nc -v $host 25`
2. `helo pop`
3. `MAIL FROM: user@domain` (this may not need to be a real user)
4. `RCPT TO: targetUser@domaain` (does need to be real)
5. `DATA`
6. 
```
Subject: RE: password reset

Hi user, 

Click this link or your skip manager gets it - http://$kaliIP/

Ragrads, 

.   
```
7. `QUIT`
8. `Bye`
