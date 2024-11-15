Starting commands:
1. `sudo nmap -p- -v -T4 -sC -A $IP --open` to reveal `$port1`, `$port2`, and so on
2. Then: `sudo nmap -sC -A -p$port1,$port2,etc $IP -T4`

- `sudo nmap -v -p- -sC -sV -T4 192.168.100.101` (Checks all ports)(T4/5 for additional speed)(-Pn to assume host is up)
- `sudo nmap -v -p- -sC -sV 192.168.100.101 -T4 -oN openports.txt && grep '/tcp' openports.txt | cut -d '/' -f 1 | paste -sd ','` (faster and echos open ports)
- `sudo nmap -sU 192.168.100.101` (Checks UDP ports specifically)

nmap flags:
-sS (SYN Scan)
-sU (UDP Scan)
-sT (TCP Scan)
-sV (Version enum)
-O (OS Fingerprinting)
-Pn (Assume host is up)
-p (Ports)
-A (runs all scans)
-n (No DNS)
-T 0-5 (Timing of scans, 0 is fastest, 3 is default)

### Print Open Ports (test)
- Print open ports: `nmap $Ip |  grep '/tcp' | cut -d '/' -f 1 | paste -sd ','`
- include the standard output in a file (scan.txt) and also the ports in a second line
`nmap $Ip -oN scan.txt && grep '/tcp' scan.txt | cut -d '/' -f 1 | paste -sd ','`
- no file creating
`output=$(nmap 192.168.247.122); echo "$output"; echo -n "Ports: "; echo "$output" | grep '/tcp' | cut -d '/' -f 1 | paste -sd ','`

### Scripting Engine
- Nmap Scripting Engine
	- The nmap scripting engine can also be used for more thorough scanning
	- Ex: `nmap --script=$script1.nse, $script2.nse $IP`
	- Find scripts: `ls /usr/share/nmap/scripts| grep $searchTerm`
	- Help on script: `nmap --script-help=$script.nse`
	- Run wildcard script: `sudo nmap --script=smb* -p 445 -Pn $IP`
	- Useful scripts from QuirkyKirkHax:
		- smb-os-discovery
		- snmp-brute * (hydra might be better)
		- smtp-brute
		- smtp-commands
		- smtp-enum-users

### nmapautomator
- Info: [nmapAutomator](https://github.com/21y4d/nmapAutomator)
- `./nmapAutomator.sh --host $Ip --type All (or Network/Port/Script/Full/UDP/Vulns/Recon)`

### autorecon
- Info: https://github.com/Tib3rius/AutoRecon