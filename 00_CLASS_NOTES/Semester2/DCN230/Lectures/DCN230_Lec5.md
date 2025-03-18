---
class: 
topics: 
tags:
  - ethernet
  - networking
creation_date: 2025-02-14 19:28
last_updated: 2025-02-14 19:28
---
#### To Do
- [ ]
---
# DCN230_Lec5
# Ethernet
![[Wk5_NB_Ch10_Ethernet.pdf]]


### Main Topic
- focus on bottom layers this lecture and the Ethernet and ARP protocols
	- physical and data link layer
	- each lecture focuses on a layer and will provide protocol examples for the layer 


### Objectives 
- describe the operation of the Ethernet sublayers
- identify the major fields of the Ethernet frame 
- describe the purpose and characteristics of the Ethernet MAC address
- compare the roles of the MAC address and the IP address
- describe the purpose of ARP
- explain how ARP requests impact network and host performance 
- explain basic switching concepts 




# _Ethernet Protocol_
---
## Sublayers
---
#### LLC sublayer


#### MAC sublayer
- destination and source MAC address
- different ways to write MAC addresses



## Ethernet Encapsulation 
---
![[Pasted image 20250215083223.png]]
ABOUT
- IEEE 802.3
	- has 'Length' field instead of 'Type' 
		- therefore cannot directly indicate what protocol is being used in the payload
	- has an 802.2 header as well as the actual data in the payload 
		- the 802.2 header inside the payload is the LLC sublayer header 
			- usually consists of:
				- DSAP (destination service access point)
					- identifies the receiving protocol
				- SSAP (source service access point)
					- identifies the sending protocol
				- Control field 
					- controls flow and acknowledgement 
		- since IEEE 802.3 does not have 'type' field, needs the LLC sublayer, in combination with the 'length' field, to determine the upper layer protocol being used 



### Ethernet Frame Size
---
- min frame size 64 bytes and max is 1,518 bytes 
	- for Ethernet 2 and IEEE 802.3
- why is there a required min size?
	- if smaller than 64 bytes, can cause collision problems 
	- therefore, anything smaller than 64 bytes is considered a 'collision fragment' or 'runt frame' 
- what happens if smaller or larger than min and max sizes?
	- receiving device drops the frame 


## Introduction to the Ethernet Frame 
---
![[Pasted image 20250215090229.png]]

Preamble and Start Frame Delimiter fields 
- NOT considered part of Ethernet frame 
	- part of layer 1
- used for synchronization between the sending and receiving devices 

Destination field
- actual start of Ethernet frame 

Length / Type field
- defines the exact length of the frame's **data field** 
- describes which L3 protocol is implemented

Data and Pad fields
- contains the encapsulated data from a higher layer
- ex: ipv4

Frame Check Sequence field
- used for error detection 
	- uses Cyclic Redundancy Check (CRC)
- if calculations match at source and receiver, no error occurred 


NOTE
- technically an Ethernet Frame does not include the 'preamble' and 'start of frame delimiter' fields 
	- preamble and start of frame delimiter are part of Layer 1 (physical) 
	- this is why in Wireshark they do not appear 
		- because they are stripped by the network interface card (NIC)
- Ethernet frame
	- starts at the 'destination address' to 'frame check' 







## Ethernet MAC (3 Types MAC Addresses)
---
### Unicast MAC Address (single destination) 
![[Pasted image 20250215091846.png]]
Unicast address
- one-to-**one** communication

In order to send a unicast message, switch must know:
- destination MAC address
- what interface to send the message out of 

To keep track of this info, the switch keeps a table with known MAC addresses and their associated interfaces 


### Broadcast MAC address (all machines in same LAN segment)
![[Pasted image 20250215091956.png]]
Broadcast address
- always FF-FF-FF-FF-FF-FF
- one-to-**all** communication 



### Multicast MAC Address (some machines in same LAN segment)
![[Pasted image 20250215092310.png]]
Multicast address
- one-to-**many** communication 

How multicast MAC addressing works
- the MAC address is not assigned to a single device, but rather represents a group of devices that 'subscribe' to the multicast 
- devices must 'subscribe' to a multicast group using IGMP 
	- Internet Group Management Protocol 
- once subscribed, the NIC listens for frames matching the multicast MAC address and forwards those packets up the network stack 
- the switch (if multicast aware) will only send multicast traffic to ports where a devices has subscribed to the multicast group 

Multicast vs Unicast MAC addresses
- unicast
	- MAC addresses assigned by manufacturers to individual devices 
- multicast 
	- follow a special 'reserved range' in the MAC address space 
		- first half of multicast MAC address ALWAYS within the range: 
			- **01**-00-5E-xx-xx-xx
			- the 01 is important 
				- least significant bit (LSB) of first byte indicates if the address is unicast (0) or multicast (1)
		- last half is derived from the IP multicast group 
			- not sure how

Common multicast examples
- streaming video/audio (IPTV, VoIP)
- routing protocols (OSPF, RIP, EIGRP all use multicast)
- gaming and real-time data applications 




# MAC and IP
---
### MAC Address
- this address does not change
- known as the 'physical' address because physically assigned to host NIC 
- *==switches==* only know how to handle MAC addresses 

### IP Address
- known as a 'logical' address because assigned logically 
	- assigned to each host by a network administrator 
- based on where the host is actually located 
- *==routers==* only know how to handle IP addresses 

NOTE
- both physical MAC and logical IP addresses are required for a computer to communicate 

![[Pasted image 20250215095632.png]]







# Intro to ARP 
---
### Purpose of ARP
- allows sending devices to determine the destination MAC address based off the known destination IP address 
- [[Address Resolution Protocol]] 

### How it works
- sending devices sends a broadcast message to all devices on the LAN segment, asking 'Who has the following IP address? if so, tell the source of this message" 
- if there is a device on the LAN segment who has the IP address, it will respond to the source of the ARP broadcast message with an ARP reply message that is unicast (one-to-one) 
	- this message will contain the MAC address associated with the destination IP address
- the MAC to IP mapping is then stored in the ARP table cache on the sending device (not switch) 
	- each devices maintains its own ARP table 
		- periodically refreshed 
	- switch does not store ARP mappings because it operates at Layer 2 and does not care about IP addresses 
		- however, a switch does keep a MAC address table which maps MAC address to switch ports 
	- to see on cisco router:
		- show ip arp 


### Problems associated with ARP
- ARP broadcasts can flood the local media 
- broadcasts, overhead on the media
- security 
	- a false ARP message can privde an incorrect MAC address that will then hijack frame using that address ([[ARP Spoofing]])

#### Mitigating ARP problems
- the switch provides segmentation of the LAN into isolated collision domains 
- the unicast communications within a LAN are isolated 
- ARP reply is only communicated between devices 

#### Example
![[Pasted image 20250215095925.png]]
- the 'Target IP address' is known, BUT the 'Target MAC' is not known 






# Switch MAC Address Table
---
### Purpose 
- switch MAC address table is used to make forwarding decisions 
- associates MAC addresses to switch port 
	- so switch knows which port to output data to get to a destination MAC
- NOTE
	- still depends on routers to pass data between IP subnets 


### How is the switch MAC address table created
- when a device sends a broadcast ARP message, it must first go through the switch 
	- the switch remembers what port received the broadcast packet and the sending MAC address in that packet and adds this data to the table 
- the switch then 'floods' the broadcast frame to all ports
	- except the port that received it 
- the destination device replies to the broadcast with a unicast frame addressed to the source of the broadcast 
	- the switch also remembers the port that received the unicast reply and the MAC address of the sender and adds this record to the MAC address table 
- the switch can now forward frames between the source and destination devices without flooding 
	- because it has entries in the address table that can identify the associated ports 










## Ethernet Examples
#### Ethernet 802.3 (IEEE 802.3)
![[Pasted image 20250215080126.png]]
ABOUT
- has 'length' field 
	- does not have 'type' field (unlike Ethernet 2)
- LLC helps determine what protocol the payload belongs to 
	- has LLC because it does not have a 'Type' field 
	- the 'Type' field in Ethernet 2 directly tells receiving device what protocol to pass the frame to 
		- ex: IPv4, arp
	- LLC uses 'Service Access Points (SAPs)' and Subnetwork Access Protocol (SNAP) to specify the protocol
- NOTE
	- IEEE 802.3 is more legacy 
	- historically used for non-ip protocols (ex: NetBIOS)


#### Ethernet 2 (more common)
![[Pasted image 20250215080439.png]]
ABOUT
- 'Type' field indicates what the payload of the frame is 
	- in this case ARP is the payload (arp request) 













# Questions
- how do the different versions of Ethernet detect the media type? 
- how do PC's learn the destination MAC address when they only have the destination IP?
	- using ARP and Ethernet broadcast



