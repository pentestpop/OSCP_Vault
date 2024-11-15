- From kali: `mysql --host $IP -u root -p$password`
	- note that there is no space between -p flag and $password
	- If you get "TLS/SSL error: SSL is required", you can append `--skip-ssl`
- Or from target: `mysql -u $user -p $database` (p flag is db password, have to enter that after)

### Commands
- `select system_user();`
- `select version();`
- `show databases;`
-` SELECT * FROM $tableName WHERE $column='$field;'`


### Brute forcing with ffuf
https://medium.com/@opabravo/manually-exploit-blind-sql-injection-with-ffuf-92881a199345