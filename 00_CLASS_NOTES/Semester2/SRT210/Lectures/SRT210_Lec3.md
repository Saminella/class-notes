---
class: srt210
topics: dhcp
tags:
  - dhcp
  - linux_networking
creation_date: 2025-01-30 10:51
last_updated: 2025-01-30 10:51
---
#### To Do
- [ ]
---
# SRT210_Lec3
# About DHCP
### Purpose of DHCP
- stands for Dynamic Host Control Protocol 
- automatically configures network devices so they can communicate over the network 
	- automatic configurations can include:
		- IP address, subnet, gateway, DNS, and other info 
- centralised management of IP addressing 

### DHCP vs Static 
- DHCP
	- centralised management of network devices IP addresses
	- keeps track of IP addresses currently in use 
		- prevents two devices from having the same address
			- can happen with static assignment
	- useful for roaming, or mobile-type devices 
		- it would be a royal pain to have to reconfigure a laptop every time you move from home to work 
- NOTE
	- can do DHCP static addressing for devices that require a fixed address (see below)

--- 

# How Does DHCP Work?
- when a device, configured to use DHCP connects to a network, it sends a general broadcast message asking for network configuration info 
	- this goes out to all devices on the physical network but is ignored by all unless it is the DHCP server 
	- this message is sent using MAC addresses, since the connecting device doesn't have an IP address yet 
		- the MAC address associated with the network card (requires wireless network card) 

### Address Allocation 
- IP address allocation == an address that has been assigned to a client by the DHCP server 
- the DHCP server has a pool of addresses and assigns one that is free
- 2 types of allocation 
	- automatic 
		- server assigns an IP address to a MAC address 
		- once associated, it is permanent 
	- dynamic 
		- server assigns an IP address to a MAC address using DHCP leasing
		- sets a timer for how long a MAC address can use an assigned IP 
		- can be renewed infinitely 
			- but if client doesn't renew, IP address is removed and added back to the pool 
	- NOTE:
		- always use dynamic unless told otherwise

### 4-Way Handshake (DORA)
##### D(iscovery)
- client sends discovery broadcast message on subnet to discover available DHCP servers 
##### O(ffer)
- DHCP server receives the request from client, reserves an IP address for client, and sends a DHCPOFFER
##### R(equest)
- client receives the offer, and sends a request asking to be assigned that offered IP address
##### A(cknowledgement) 
- DHCP server receives the request and sends the client an acknowledgement that they can use that IP address
- the client then applies this address to itself
https://www.intraway.com/blog/dhcp-differences-with-and-without-relay-agent/
- a VERY helpful resource 

---

# How To Setup and Configure a DHCP Server 
- DHCP server package for Ubuntu is 'isc-dhcp-server'
	- other distros may use a different package name 
- main config file is:
	- /etc/dhcp/dhcpd.conf
	- contains:
		- global settings
			- options that apply throughout the entire network 
		- subnet declarations 
			- options that apply only to that subnet 
	- changes to this file require the dhcp service to be restarted 
		- sudo systemctl restart isc-dhcp-server
- NOTE:
	- DHCP prioritizes MAC address matching over giaddr 

### DHCP Global Options
- Common global options include:
	- IP address range 
		- allows you to specify the range of addresses for your DHCP pool 
		- start and end addresses
	- Lease duration 
		- set the default and max lease time for clients
	- Router (default gateway) 
		- optional 
		- sets the default gateway for clients 
	- Domain Name and DNS servers 
		- optional 
		- assins the parent domain and the addresses of DNS servers to the client 

##### Lease Duration
- refers to the amount of time an IP address is pared with a device 
- once that time passes, one of two things can happen
	- client asks to renew lease (resets counter)
	- no renewal is requested 
		- IP address will be disassociated with MAC address and returned to pool
- to control lease duration
	- lease time written in **seconds** 
	- 2 main types of lease duration settings:
		- default-lease-time
			- if client doesn't specify duration during handshake, its giving this time
		- max-lease-time
			- if client does specify duration, this is the max amount of time it can have 

### DHCP Static Addressing 
- the 3rd type of IP address allocation a DHCP server can provide 
- 'fixed addressing' (or reservations in Windows) allows you to associate a particular IP address with a particular device 
- similar to a static network configuration 
	- difference is you're specifying network info using the DHCP server 
- the device that's assigned a fixed address still goes through the DHCP handshake process, but will still always receive the same address 
	- and no one else can receive that address ('reserved')

---

# DHCP Troubleshooting
- troubleshooting DHCP daemon is an important part of working with the service 
- if changes have been made to the config file and restarted, first step is to check the service is running properly 
- check status:
	- sudo systemctl status isc-dhcp-server
- watch dhcpd status messages in real-time:
	- sudo journalctl -xefu isc-dhcp-server 
- other config files to check while troubleshooting
	- /var/lib/dhcp/dhcpd.leases
		- this file on the server holds a record of leased addresses 
		- if the service (or server computer) is restarted, it reads this file to know what's been assigned 
	- /var/lib/dhclient/
		- this directory on the client stores its record of leases 
- most common problem
	- typos in the config file 
		- pay special attention to semicolons! 

---

# DHCP Client Control 
- on your client machine, there are 3 ways to get it to run through the 4-way DHCP handshake 
	- a system reboot
	- bringing an interface down, then up again
	- running this command: dhclient
		- make sure the appropriate interface is set to request a dhcp address, or the command will not do anything 



https://documentation.ubuntu.com/server/how-to/networking/install-isc-dhcp-server/ 

# DHCP Relays
## Purpose of DHCP 
- although DHCP servers can provide IP addresses for one subnet or multiple subnets HOWEVER to do so for multiple subnets requires a DHCP Relay
- DHCP Relay agents allow DHCP clients to communicate with DHCP servers not on their subnets 

### How do Relay Agents Work
- DHCP Relay Agents are installed on these subnets 
	- run on a network device, capable of routing between the client's subnet and the subnet of the DHCP server (aka Windows Server 2019)
- DHCP client sends broadcast on local link > relay agent receives the broadcast > transmits it to one more DHCP servers 
- DHCP relay agents work at [[Network Layer|Layer 3]] and don't rely on having an IP assigned to function 
	- capture broadcast DHCP discover packets at [[Data Link Layer|Layer 2]] (Ethernet) and forward them as a unicast packet with a subnet identifier 

![[Pasted image 20250130125621.png]]


![[Pasted image 20250130125633.png]]




When troubleshooting correct IP addresses
- good thing to know all the places that require an IP address
- can very quickly narrow down problem 





