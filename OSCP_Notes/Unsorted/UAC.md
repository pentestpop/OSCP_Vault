To confirm if it's enabled:
```
REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v EnableLUA
```
- If there is a 1, it is. If there is a 0, it's not. 

Check which level is configured:
```
REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v ConsentPromptBehaviorAdmin
```

- If `**0**` then, UAC won't prompt (like **disabled**)
    
- If `**1**` the admin is **asked for username and password** to execute the binary with high rights (on Secure Desktop)
    
- If `**2**` (**Always notify me**) UAC will always ask for confirmation to the administrator when he tries to execute something with high privileges (on Secure Desktop)
    
- If `**3**` like `1` but not necessary on Secure Desktop
    
- If `**4**` like `2` but not necessary on Secure Desktop
    
- if `**5**`(**default**) it will ask the administrator to confirm to run non Windows binaries with high privileges

