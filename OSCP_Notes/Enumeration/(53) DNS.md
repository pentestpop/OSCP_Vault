DNS Enumeration might give you information on other hosts in the network.
Keep in mind, you will probably have to mess with /etc/conf for this!!!

If you are looking for DNS servers specifically, use nmap to quickly and easily search:
`nmap -sU -p53 ​$network`

Normal DNS Query:
`nslookup ​$IP`

Query for MX Servers within a domain:
`dig ​$domain ​MX`

Query for Name Servers within a domain:
`dig ​$domain ​NS`

DNS Zone Transfer (This will give you all of the marbles!)
`dig axfr @​$nameServer $domain`
`dnsrecon -d ​domain ​-a --name_server ​server`

If you want to brute force subdomain enum, try dnsmap:
`dnsmap ​$domain`