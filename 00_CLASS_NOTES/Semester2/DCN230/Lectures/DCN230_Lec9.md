---
class: dcn230
topics: 
tags:
  - routing
  - cisco
  - school/lecture
creation_date: 2025-03-20 15:49
last_updated: 2025-03-20 15:49
---
#### To Do
- [ ]
---
# DCN230_Lec9
# Routing Concepts
![[Wk10_Routing Concepts.pdf]]


>[!warning] 
>This lecture is very important. Must understand these routing concepts.


### Main Topics
- [[#1️⃣ Path Determination]]
	- how do routers determine the best path
- [[#2️⃣ Packet Forwarding]]
	- how do routers forward packets to the destination
- basic router configuration review
	- configure basic settings on a router
- [[#3️⃣ IP Routing Table]]
	- describe the structure of a routing table
- static and dynamic routing
	- compare static and dynamic routing concepts


### Main Purpose of this Lecture
- understand how routers use information in packets to make forwarding decisions



# 1️⃣ Path Determination 
---
- when a router receives an IP packet on one interface, it determines which interface to use to forward the packet to the destination 
	- AKA routing
- the interface the router uses to forward the packet can be: 
	- the final destination OR
	- a network connected to another router that is used to reach the destination network 


---

### 2 Primary functions of a router
###### 1. Determine the best path to forward packets based on the information in its routing table 
- how
	- an admin manually enters routing info
	- router learns by itself
- done before packet is forwarded 
- routers use the routing table like a map to discover the best path for a given network

###### 2. forward packets toward their destination 
- involves re-encapsulation 

>[!note]
>even without any routing configurations or learning dynamically, when a router interface is turned on and assigned an IP address, an entry is automatically added to the routing table for the network ID of the interface just turned on. also known as 'directly connected network'
>- considered a 'directly connected network'




---

## Best Path Equals Longest Match
- what is meant by 'the longest match' 
	- router examines the *destination IP* of a packet 
	- whatever path is most specific 
- this would be considered the best path from the packets perspective



#### Proof
- say a router receives a packet that has a destination going to `172.16.0.10`
- based off the below table, it will select the 3rd route to `172.16.0.0/26`
	- because it has the longest match when both addresses are compared in binary


destination IP in binary: 10101100.00010000.00000000.00001010

| Index | Routing Entry | Address in Binary                       |
| ----- | ------------- | --------------------------------------- |
| 1     | 172.16.0.0/12 | ==10101100.0001==0000.00000000.00000000 |
| 2     | 172.16.0.0/18 | ==10101100.00010000.00==000000.00000000 |
| 3     | 172.16.0.0/26 | ==10101100.00010000.00000000.00==000000 |
- although all routing entries match, the 3rd one has the *longest* match in binary
	- it is the most specific



>[!note]
>if a packet matches with more than one identical route where it can't be decided which is the best, it will send split the packet up and send it over both
>- but many other factors are considered before this (ex: administrative distance, metric) 


---

## Build the Routing Table 
#### 3 Categories of entries in a routing table
- these are the 3 ways in which a router can learn routing info
	- how entries are added to a routing table

###### Directly connected networks 
- added to the routing table when a local interface is configured with an IP address and is active (up and up)


###### Remote networks 
- can be added via: 
	- static routes 
		- added manually 
	- dynamic routing protocols 


###### Default route 
- also referred to as 'the gateway of last resort' 
- specifies a next-hop router to use when the routing table does not contain a specific route that matches the destination IP address 
- can be added via: 
	- manually added static route
	- dynamic routing protocol
- default route always has a /0 prefix 
	- because no bits need to match the destination IP address for this route entry to be used 


>[!note]
>if a machine does NOT have a default route, traffic will be *dropped*

---


# 2️⃣ Packet Forwarding 
---
![[Pasted image 20250320195222.png]]

- when a packet arrives at a router, it follows the above flow chart 
- source IP address is never changed by router 
	- layer 3

NOTE
- the source and destination MAC addresses will change every time data is re-encapsulated by a router 
	- the frame header and trailer only exist on the local network 
		- because they are layer 2 and their purpose is to facilitate data transfer over different medium
		- if the data was received via wireless connection and sent via Ethernet, this requires different frame header/trailer info 
	- for a given local network 
		- the MAC source is the source of the router and destination MAC is the next router hop
	- will change every time the packet is re-encapsulated 

>[!note]
>the source and destination MAC addresses will change every time data is re-encapsulated by a router 
>- the frame header and trailer only exist on the local network 
>	- because they are layer 2 and their purpose is to facilitate data transfer over different medium
>	- if the data was received via wireless connection and sent via Ethernet, this requires different frame header/trailer info 
>- for a given LAN:
>	- the MAC source is the source of the router and destination MAC is the next router hop will change every time the packet is re-encapsulated 

---

### End-to-End Packet Forwarding 
- ==primary responsibility of the packet forwarding function is to encapsulate packets in the appropriate data link frame type for the outgoing interface== 
- ex: 
	- the data link frame format for a serial link could be Point-to-Point (PPP) protocol, High-Level Data Link control (HDLC) protocol, or some other layer 2 protocol 


---

### Forwarding the Packet to a Device on a **Directly Connected** Network 
- if the route entry indicates the egress interface is a directly connected network, the packet can be forwarded directly to the destination device 
	- typically an Ethernet LAN 
- in order to encapsulate the packet in the Ethernet frame, the router needs to the determine the destination MAC address associated with the destination IP address of the packet 
	- via ARP if does not already know
- similar to `On-Link` in Windows [[Windows Server Routing]] 


### Forwarding the Packet to a Device on a **Next-Hop Router** 
- if route entry indicates destination IP address is on a remote network = packet must be forwarded to the next-hop router
	- next-hop address indicated in route entry 
- how: 
	- if forwarding and next-hop router on same Ethernet network
		- ARP and ICMPv6 Neighbour Discover will occur to determine destination MAC address 
	- similar to when a router forwards a packet to a directly connected network HOWEVER, router first consults its ARP table or neighbour cache to find the IP address of the next-hop router
		- with direct connection, destination IP is in packet


### Drop the Packet (No Match in Routing Table)
- if there is no match between the destination IP address and a prefix in the routing table, and there is no default route == packet is dropped

---


# 3️⃣ IP Routing Table 
---
### Route Sources 
- routing table contains list of routes to known networks 
- the source of this info is derived from one of the following:
	- directly connected networks
	- static routes 
	- dynamic routing protocol 


| Code | Description                                                                              |
| ---- | ---------------------------------------------------------------------------------------- |
| L    | address assigned to a router interface (direct connected network)                        |
| C    | indicates a directly connected network                                                   |
| S    | identifies a static route created to reach a specific network                            |
| O    | identifies a dynamically learned network from another router using OSPF routing protocol |
| \*   | this route is a candidate for a default route                                            |

---

### Routing Table Principles 
(very important) 

###### 1. Every router makes its decisions alone, based on the info it has in its own routing table
- ex: 
	- R1 can only forward packets using its own routing table 
	- R1 does not know what routes are in the routing tables of other routers (like R2)

###### 2. The info in a routing table for one router does not necessarily match the routing table of another router 
- ex: 
	- just because R1 has a route in its routing table to a network  in the internet via R2, does NOT mean R2 knows about that same network 

###### 3. Routing info about a path does not provide return routing info
- ex: 
	- R1 receives a packet with the destination IP address of PC1 and the source IP of PC3
	- just because R1 knows to forward the packet out of its G0/0/0 interface, does NOT mean it knows how to forward packets originating from PC1 back to the remote network of PC3
- this is why pinging does not work if there are only routes going in one direction 
	- if there is a router between two devices, router must have routes going both ways for pinging to work for one pc
		- else the return ping will not work 


>[!tip]
>these Routing Table Principles are very important



---

### Routing Table Entries 
![[Pasted image 20250320204322.png]]

1. route source
	1. how the route was learned 
	2. ex: O, L, C, S etc..
2. destination network (prefix and prefix length)
	1. the address of the remote network
	2. NOTE:
		1. prefix length of destination network specifies the min number of far-left bits that must match between the IP address of the packet and destination network (prefix) for this route to be used 
3. administrative distance 
	1. the trustworthiness of the route source 
		1. based on the route source 
	2. purpose: 
		1. helps router select best route
	3. lower values = preferred route source 
	4. in the above example the, administrative distance is 110
	5. ![[Pasted image 20250320205057.png]]
		1. directly connected is the most trustworthy 
4. metric
	1. the value assigned to reach the remote network 
		1. determines the 'cost' 
	2. purpose:
		1. if the administrative distance is the same, then metric is considered 
	3. lower values = preferred routes
	4. in the above example, the metric is 50
5. next-hop
	1. the IP address of the next router to which the packet would be forwarded 
6. route timestamp 
	1. how much time has passed since the route was learned 
7. exit interface 
	1. the egress interface to use for outgoing packets to reach their final destination 


---

### Static Routes
- not automatically updated
	- must be manually reconfigured if network topology changes

##### 3 primary uses of Static Routes
- easy routing table maintenance in small networks 
	- that are not expected to scale up 
- to configure a single default route 
	- `0.0.0.0 0.0.0.0` ...
- for routing to and from 'stub networks'
	- a network accessed by a single route, and the route has only one neighbour 

###### Static route syntax
```C++
R1(config)# ip route 10.0.4.0 255.255.255.0 10.0.3.2
```
- `10.0.3.2` is the next hop 

---


### Dynamic Routing Protocol 
![[Pasted image 20250320210752.png]]
- dynamic routing protocols are used by routers to automatically share info about the reach-ability and status of remote networks 
- dynamic routing protocols perform several activities 
	- including network discovery and maintaining routing tables 































