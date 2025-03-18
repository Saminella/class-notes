---
class: dcn230
topics: 
tags:
  - networking
  - WAN
  - LAN
  - cisco
  - networking/design
creation_date: 2025-01-11 09:45
last_updated: 2025-01-11 09:45
---
#### To Do
- [ ]
---
# DCN230_Lec1
![[Wk1_NB_Ch1_Intro.pdf]]



## What is Communication?
- Have to establish rules for effective communication
	- an identified sender and receiver
	- agreed upon method of communicating 
	- common language and grammar
	- speed and timing of delivery 
		- timing rules
		- devices give a 'timeout' 
			- if a timeout is exceeded, the device will assume the packet is lost 
	- confirmation or acknowledgement required 
		- TCP vs UDP
		- ex: if browser does not receive the full webpage from the server, will get error message that server is down 
		- some applications rely on 'reliability' (ex: apps that use TCP) but others do not (ex: UDP streams)
- ==for devices to communicate, have to establish rules == PROTOCOLS==
	- communication protocols 
		- there are standardized documents that say the specifics of the protocol
		- extremely standardized 
			- because lots of vendors that make devices which need to communicate 
			- standards allow different manufacturers to make products that are compatible with each other 

## Quality of Communicating 
- External factors affecting the success of communications include:
	- the pathway quality between the sender and receiver
	- number of times the message must change form 
	- number of times the message must be redirected or readdressed
	- number of times the other messages transmitted simultaneously on the communication network 
		- previously
			- 'Multiple networks'
			- each different type of communicated used to have its own dedicated network 
			- ex: computer networks, telephone networks, and broadcast networks were all separate 
		- currently 
			- 'Converged networks'
			- now, all different applications use the same network structure 
				- all use the same cable to communicate/send data 
			- therefore, have to prioritize what data to send when, and how that data will be processed 
				- ex: real-time data VS non real-time data => real-time data should be processed first because non real-time data can handle delay better
	- amount of time allocated for successful communication 
- Internal factors affecting successful communication across the network include:
	- message size
		- smaller messages are delivered more easily and reliably
	- message complexity 
		- simple messages are interpreted more easily 
	- message importance
		- important messages must be handled differently and have priority 
		- determined by networking device(s)


## 4 Major Considerations When Designing a Network
- As networks evolve, we are discovering that there are four basic characteristics that the underlying architecture much address to meet user expectations:
#### 1. Fault tolerance
- Network is as resilient as possible 
- Backups
	- infrastructure back plan 
	- software backup
	- and data backups 
		- includes:
			- central data centre storage
			- off location data storage 
![[Pasted image 20250117083302.png]]
- in this example:
	- if one router goes down, network is still connected to internet 
		- only possible if there are redundant routers 
	- if all routers are up and running
		- the routers can share the load to improve network performance 
	- switches should also have redundant connections 
		- not just routers 
![[Pasted image 20250117084031.png]]
- in this example:
	- ==packet switching== 
		- networking devices determine the best route from source to destination 
	- another example of redundant routers 
	- the packet is broken into parts which take different paths to the destination. Then the parts are brought back together and presented to recipient 
![[Pasted image 20250117084120.png]]
- in this example
	- ==circuit switching== 
		- the path is predetermined (from source to destination) 
		- no freedom for data to flow by itself based off best route 
		- instead, a virtual link is setup from source to destination
			- data only flows through this virtual link 
	- can see that the devices along the dedicated path are specifically used for this line of communication 
		- can use for secure communication channels? 

#### 2. Scalability 
- Allow networks to grow 
- These days there are a LOT of devices connected to networks 
![[Pasted image 20250117084408.png]]
- in this example:
	- Tier-1 ISPs 
		- form the centre of the internet
			- top level 
		- they have 'full mesh' communication 
	- Tier-2 ISPs
		- regional service ISP
		- connect local ISPs to Tier-1 ISPs
			- usually pay Tier-1 ISPs for connectivity to rest of internet 
	- Tier-3 ISPs
		- local ISP providers
		- connect directly to end users 
#### 3. QoS (Quality of Service)
- some require lower quality of service compared to others 
- ex: 
	- streaming = less important 
- examples of priority decisions for an organization might include:
	- time-sensitive communication 
		- an increase in priority for services like telephony or video distribution 
	- non time-sensitive communication 
		- a decrease in priority for web page retrieval or email 
	- high  importance to organization 
		- an increase in priority for production control or business transaction data 
	- undesirable communication 
		- a decrease in priority or block unwanted activity 
		- ex: peer-to-peer file sharing, live entertainment 

##### Example: Traffic Prioritization 
![[Pasted image 20250117085403.png]]
- in this example:
	- data is sent from end devices to a router 
	- router sorts packets based on priority and sends them based off this queue 
#### 4. Security 
- some protocols were not designed with security in mind 
	- ex: Telnet 
- therefore, have to implement controls to compensate for those vulnerabilities 
	- ex: firewalls, high level security protocols, implement security for the different OSI layers 
- CIA
	- Confidentiality 
		- only intended and authorized recipients should be able to access and read data 
		- use user authentication and data encryption 
	- Integrity
		- assurance that the info has not been altered in transmission 
		- validate data (CRC, message digest, .. )
	- Availability 
		- assurance of timely and reliable access to data services for authorized users 
		- use firewalls, malware protection, IDS


## LANs, WANs, and Internet 
### Components of a Network 
- 3 categories of network components:
	- Devices
		- ex: switches, routers, end devices, servers, etc.. 
	- Media
		- how devices are connected together 
		- ex: ethernet links, wi-fi links, etc.. 
	- Services 
		- some networking devices serve 'networking services' 
		- other devices, like servers, provide services like web, email, database etc.. 


#### Cisco Network Representations
![[Pasted image 20250117103439.png]]

#### About Network Media
- network media is the physical or wireless means through which data travels in a network. It is the medium that carries signals between devices
- types of network media
	- Wired (LAN media)
		- ethernet cables 
			- ex: twisted pair, coaxial, fiber optic, etc..
		- typically used in local area networks 
	- Wireless (Wireless Media)
		- Wi-fi (radio waves), bluetooth, infrared, satellite 
		- used in both LAN and WAN networks 
	- WAN Media
		- Fibre optic, microwave, satellite, cellular networks (4G, 5G)
		- used in wide area networks to connect geographically distant locations 
- NOTE
	- although LAN and WAN are network types, LAN media and WAN media refer to the physical or wireless technologies used in these networks 










### Physical Topology vs Logical Topology 
![[Pasted image 20250117103539.png]]
Physical topology 
- informs where cables go and where devices are located 
- useful when duplicating the same network in a different location 

Logical Topology 
- conceptual design 
- only tells you the functionality of the network and the protocols used 


### LAN (Local Area Network)
- a router is required to route traffic from LAN to LAN 
	- therefore a router is the delimiter between where one LAN starts and another ends 

### WAN (Wider Area Network)
![[Pasted image 20250117104252.png]]

### Internet 
![[Pasted image 20250117104527.png]]

### Wireless LAN 
- connecting device to switch via AP (access point)
	- instead of connecting directly to switch (via ethernet) 

### Storage Area Network (SAN)
- specifically designed for storing data 

## New Trends and Challenges 
- Some top trends include: 
	- Bring Your Own Device (BYOD)
		- how to design security policies?
	- online collaboration 
	- video 
	- cloud computing 

#### Cloud Computing 
- cloud computing offers the following potential benefits 
	- organizational flexibility 
	- agility and rapid deployment 
	- reduced cost of infrastructure 
	- refocus of IT resources 
	- creation of new business models 
- cloud service providers have to build big 'data centres' to support this
	- redundant data communications connections 
	- high speed virtual servers 
		- sometimes referred to as server farms or server clusters 
	- redundant storage systems 
		- ex: SAN technology 
	- redundant or backup power supplies 
	- environmental controls 
		- ex: air conditioning, fire suppression 
	- security devices 


