To crack the entry password:
- `keepass2john Database.kdbx > Database.hash`
- then `john --format=keepass Database.hash` for entry password
- then `kpcli --kdb Database.kdbx`
	- then `ls` `cd $Directory` and `show "$Full Entry"