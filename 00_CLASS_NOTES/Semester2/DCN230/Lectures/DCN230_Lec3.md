---
class: 
topics: 
tags: 
creation_date: 2025-01-23 08:08
last_updated: 2025-01-23 08:08
---
#### To Do
- [ ]
---
# DCN230_Lec3

# Slides
![[Wk3_NB_Ch3_Protocols and Networking Models.pdf]]



## Models
- we require models for devices to be able to communicate 
- what 'models' are important for this course? 
	- OSI and TCP/IP
	- NOTE:
		- TCP/IP is also considered a protocol stack, in addition to it being a network model 
- what protocols define what rules 
	- so devices that use the same protocol can communicate with other devices that also use the same protocol 
- why?
	- data that travels must travel through many different types of devices before reaching destination 
	- these devices must support data forwarding and data process 
		- ex: end devices 
			- must know how to generate data
		- ex: receiver
			- must know how to process data
		- ex: middle devices
			- must know how to forward data 
	- to break down complicated communications: protocol suite
		- separate a big complicated task into small standards 
		- at each layer, there are multiple protocols that server the layer 

![[Pasted image 20250123081735.png]]
- can see that there a individual protocols that operate at each layer 
- the protocols at each layer must pass the data to the upper or lower layers 
	- responsible for certain tasks at their layer, then pass to the next layer for further processing 
- what are some important protocol suits for cybersecurity?
- do developers specify the exact protocols their applications will use from the protocol stack? or are they dynamically determined by the machine? 

## How is data sent
- data is usually broken into smaller parts called 'segments' 
	- some parts of the segment may get lost in transmission, but these segments can be resent quickly 
	- but without segmentation, if data is sent as a big chunck and data is lost, all data is lost which would require all the data to be sent again 
		- less effective 
- how is data put together by receiver 'resegmentation' 
	- have to provide 'sequence numbers' for each segment 
	- allows receiver to reassemble segments into correct order
- cost benefit 
	- more complex 
	- more effective 

![[Pasted image 20250123082623.png]]
- ex: email protocol suite #review
	- Application layer 
		- requires TCP because emails need reliability 
	- therefore, data is TCP encapsulated 
	- then IP header is added 
	- IP unit then pushed to network access layer 
		- ethernet header attached at beginning and ethernet trailer attached at end = frame
- #review data Protocol Data Units (PDUs)
	- ex: packet, frame, segment 
	- know what info is part of each data unit 
		- ex: IP header and trailer, frame header and trailer, etc.. 

![[Pasted image 20250123083141.png]]
- done on receiver side 
- a single machine can be doing both encapsulation and decapsulation
	- can be doing multiple channels of communication at same time



#### What happens when the destination is on a different network than source?
- since this would require a gateway, the destination MAC address will be affected 
	- for every hop, the destination MAC address in the frame header will be different 
	- because MAC addresses are layer 2 (handled by switches) but to travel to a different network requires a layer 3 devices (router) 
	- therefore, for each hop the destination MAC address will be the next hop's MAC address and the source MAC address will be the current hop's MAC address 
	- how does ARP fit into this?
		- if the router does not know the MAC address of the next hop, it will use ARP
			- routers also maintain an ARP cache 
		- ex: router hasn't communicated with next hop recently or the next hop changes (ex: dynamic routing updates)
- however, since IP addresses are layer 3 the destination and source IP addresses remain the same through all the hops 
	- each hop (router) decapsulates the packet from layer 1 to layer 3, inspects the header to see what the destination IP is, determines the best next hop, then encapsulates the packet again and sends it to the next hop

##### What constitutes a 'different network'?
Inter-subnet routing 
- when traffic moves between different subnets 
	- usually just called 'routing' 
- always requires a layer 3 devices 
- even if two subnets are part of the same physical infrastructure, they are separate networks at layer 3 
	- and are therefore considered separate networks 

Inter-vlan routing 
- when traffic moves between different VLANs 
	- since the are part of the same subnet, they are considered part of the same network
- since VLANs operate at layer 2, a router or layer 3 switch must handle traffic between them 
- even if two VLANs share the same subnet, traffic must still be routed
	- but this is bad network design 





![[Pasted image 20250123085148.png]]
- router can only support layers 1-3
- but PC a and b can support all layers 
- so the PC A and B will send data all the way down to layer 1 then send to router 1 > router 1 will bring data up to 3 layer to know what IP destination then re-encapsulate and send to next hop which will do the same until destination is reached 
	- destination MAC address from device A can only go as far as router 1
		- 
	- destination MAC address from router 1 can only go as far as router 2
	- destination MAC address from router 2 can only go as far as Device B
		- now destination IP address and destination MAC address both point to same device 








