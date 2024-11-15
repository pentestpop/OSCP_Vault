A Linux PATH vulnerability typically arises when a malicious user is able to exploit the environment variable `PATH` to execute unintended commands. This is especially problematic when scripts or programs with elevated privileges (like root) inadvertently execute malicious code instead of legitimate system binaries. Here's a classic example of such a vulnerability:

### Linux Example: 
Misconfigured PATH in a Privileged Script Scenario:

Imagine there's a script that is run by the root user or by a setuid root binary. This script includes a line that calls a common command like `ls` without specifying the full path (e.g., `/bin/ls`). The script assumes that the `ls` command is being run from `/bin/ls`, but it doesn’t explicitly set the `PATH` variable.

#### The Script 
(`/usr/local/bin/example_script.sh`):
```
#!/bin/bash

# The script assumes the `ls` command is safe to run without full path.
ls /important_directory

```

#### Vulnerability

If an attacker can influence the `PATH` environment variable (perhaps by modifying it before the script runs), they could replace the `ls` command with a malicious one.

For example, the attacker might do the following:

1. **Create a Malicious Script**: The attacker creates a script named `ls` in a directory they control:

```
#!/bin/bash
echo "Malicious ls executed!"
# Potentially harmful actions could be added here
```

2. **Modify the PATH**: The attacker then modifies the `PATH` variable to include the directory containing the malicious `ls` script before `/bin`:

```
export PATH=/home/attacker:$PATH
```

3. **Execute the Vulnerable Script**: When the vulnerable script (`example_script.sh`) is executed by root, it searches for `ls` in the directories listed in `PATH` in order. Since the attacker’s directory is listed first, the script will execute the malicious `ls` instead of the legitimate `/bin/ls`.


### Windows Example

#### Scenario:

Consider a scenario where a privileged Windows service or script is executed with administrator rights. The script calls common Windows commands, such as `net.exe` (used for managing network settings) without specifying the full path (e.g., `C:\Windows\System32\net.exe`).

If an attacker can control the `PATH` environment variable, they can place a malicious executable named `net.exe` in a directory that appears earlier in the `PATH` order, causing the system to execute their malicious code instead of the legitimate system command.

#### Vulnerable Script or Service:
```
@echo off

rem The script attempts to add a user to the Administrators group
net localgroup Administrators MaliciousUser /add
```

#### Vulnerability:

If the script does not specify the full path to `net.exe`, it will search for `net.exe` in the directories listed in the `PATH` environment variable. An attacker could exploit this by doing the following:

1. **Create a Malicious `net.exe`**: The attacker creates a malicious `net.exe` that performs unintended actions, such as creating a backdoor user or downloading and executing malware.
    
2. **Modify the PATH**: The attacker modifies the `PATH` environment variable to include a directory they control at the beginning of the `PATH` order. This directory contains their malicious `net.exe`.

```
set PATH=C:\Users\Attacker\malicious_directory;%PATH%
```

**Execute the Vulnerable Script**: When the vulnerable script runs, it uses the `PATH` variable to locate `net.exe`. Since the attacker's directory is listed first in `PATH`, the system will execute the malicious `net.exe` instead of the legitimate one located in `C:\Windows\System32`.