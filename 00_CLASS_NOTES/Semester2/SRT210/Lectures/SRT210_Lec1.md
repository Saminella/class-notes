---
class: srt210
topics: routing
creation_date: 2025-01-13 08:01
last_updated: 2025-01-13 08:01
---
#### To Do
- [ ]
---
# SRT210_Lec1

![[AddressingAndRouting.pdf]]


# Gathering Info 
- before making any changes GATHER INFO
- as an admin, probs lots of machines you're responsible for (impossible to know everything) also other admins may have made changes

#### Gathering Networking Info 
- best tool: ip 
	- different variations 
- ex: 
	- ip address show 
		- this is the FULL version of ip command 
			- best option, shows more details 'ip -br a' 
			- other variations: ip address, ip a
		- // will show all the networking interfaces and info about them 
		- MAC address 
		- IPv4 and IPv6 ip addresses 


# Netplan Files 
![[Pasted image 20250113081007.png]]
- numbered 
	- lower numbers are applied first 
	- if going to describe changes for an interface, do all in the same file 
- best practices 
	- don't make new config files if not necessary 

### Netplan File Syntax
- YAML very specific about syntax and spacing 
- all indentation and spacing has to be exact same
- example:
```yaml
network:
	version: 2
	ethernets:
		ens33:
```

#### Interface Entries 
- each interface can have several possible subordinate entries
	- ex: IP configurations, routing for each interface 
- to set static or dynamic
```yaml
	ens33:
		dhcp4: false
		addresses: 
		 - ip address 
	ens34:
		dhcp4: true
```
- NOTE
	- ==if you set a static IP but don't set dhcp4 to false, will have 2 IP addresses==!!

### Applying Changes 
- have to tell machine to re-read config file 
- depends on the tool you are using 
- to apply changes for Netplan: 
	- sudo netplan apply 
	- // will read config files in numerical order and apply them and will also provide feedback if errors

# Routing 
- purpose: 
	- to know how to handle traffic once it leave the machine 
- with DHCP, usually don't have to configure this 
	- all configurations are assigned automatically by DHCP server 
- in a simple network 
	- all machines in a small internal network should know each other and what the gateway is 
	- gateway is like the path out of the internal network 
		- how the internal machines can access the gateway 
	- ==if there is traffic destined for an IP address outside of the internal network IP range, will go through the gateway==
		- hence the name 'default' gateway
- in more complex, larger networks 
	- there are usually layers of the network 
	- have to know the path through the layers to get to the internet 
		- to get through the intermediary networks 
		- ex: might have to pass through multiple subnets 
	- might have more routers within a network 
- default route 
	- to internet 
- other routes 
	- to internal network 
- What is the difference between the default route and other routes? #question
	- default route is the gateway to the internet 
	- usually, a machine will only have 1 default route
		- but if a machine has interfaces in two or more networks that have multiple viable paths to the internet, it could hypothetically have multiple default routes
### How to Configure Routes
- for a route to be added to your list, need to enter it in the netplan file 
- add an entry called 'routes', subordinate to interface name 
- take a list of pairs, each with 'to' and via' 
	- to - destination machine or network (CIDR)
	- via - intermediary to send traffic to in order to reach that network 
- example:
```yaml
ens1:
	routes:
		- to: 172.16.0.0/24 # network/subnet ID
			via: 192.168.30.5 # gateway for that network
```
- NOTE:
	- when you execute sudo netplan apply and have made changes, will remove the automatic routing routes 
		- therefore, ==if execute sudo netplan apply, have to set the default route and all other routes to internal network(s) that are required==

##### To get routing info 
- ip route show 
	- displays a list of routes this machine knows about 
	- look into! cool 
	- will show the IP address and the interface used to connect to the gateway(s) 
- should automatically see route 
- for all routing traffic that does not match with an explicit routing rule, it will be sent through the default route (to internet) 
	- default is the only one that goes to the internet


#### Traceroute
- once reach public network, will not get response back from hopes 
- shows all the routers a packet touches on its path to the destination 


### DNS Client Settings
- if using DHCP, probably configured automatically 
	- but may want to override these default configs 
- to control where DNS queries 
- to see current settings: 
	- /etc/resolv.conf 
		- // can look at this file BUT do not edit 
		- // changes will be overwritten with every netplan apply 
	- best to not override configurations in this file 
- in Ubunutu, uses systemd-resolved which acts as a DNS server by passing traffic to a real DNS server 
	- default 
	- to check if this is the case: 
		- if /etc/resolv.conf shows 127.0.0.53 then yes 
- to make changes to systemd-resolved
	- /etc/systemd/resolved.conf 
	- // Domains 
		- will auto complete domain names for you 
		- ex: when you ssh to 'matrix' on campus but have to ssh to matrix@senecapolytechnic when at home
- use ==resolvectl== to check what DNS server the machine resolves to
- NOTE:
	- if you set the DNS settings in the interface YAML file, will not apply if systemd is currently running 
		- have to disable for DNS configs to take effect 

### If you have a DHCP address but want to manually assign DNS
```YAML
ens1
```


#### For benchmarks
https://learn.cisecurity.org/benchmarks?_gl=1*1hhkwd5*_ga*ODcyMDY2NjQzLjE3MzY3Nzg2NDc.*_ga_3FW1B1JC98*MTczNjc3ODY0Ni4xLjAuMTczNjc3ODY0Ni4wLjAuMA..*_ga_N70Z2MKMD7*MTczNjc3ODY0Ni4xLjAuMTczNjc3ODY0Ni42MC4wLjA.
- chapter 3 
	- ways to increase security 

forwarding traffic not good, but for gateways in lab2 will be required 