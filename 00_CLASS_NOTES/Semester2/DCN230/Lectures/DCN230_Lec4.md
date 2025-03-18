---
class: 
topics: 
tags:
  - layer2
  - data_link_layer
creation_date: 2025-02-06 12:25
last_updated: 2025-02-06 12:25
---
#### To Do
- [ ]
---
# DCN230_Lec4

# Slides pt1 
![[Wk4_Part1_NB_Ch9_Network Access.pdf]]


# Slides pt2
![[Wk4_Part2_Network Design.pdf]]


# Data Link Layer
### Purpose 
- main role of data link layer is to control access to the media of transmission 
	- therefore, all layers above don't need to worry about the media 


## Data Link Sublayers
![[0ULQ3cAMiVcTcpUpTVa6vA.webp]]

Within the Data Link layer are two 'sublayers'
- LLC Sublayer (Logical Link Sublayer)
	- faces 'up' towards higher layers 
	- determines what protocols were used in upper layers 
		- ex: UDP, TCP, IP, ...
- MAC Sublayer 
	- faces 'down' to the actual media of transmission (ex: ethernet, wifi, bluetooth)

NOTE
- in Wireshark, the order of these two layers is opposite
![[Pasted image 20250206124421.png]]
- can see that 'IEEE 802.3 Ethernet' (MAC sublayer) is above the 'Logical-Link Control' sublayer, even though it comes after 


# Media Access Control 
- the purpose of the Data Link layer 
- the problem:
	- many different types media data can travel on
	- how can data reliably reach destination with all these different media and devices?
		- Data link layer protocols address this by governing how to format a 'frame' for use on different media
- ==at each hop, an intermediary device accepts frames from one medium, decapsulates the frame and then forwards the packets in a new frame== 
	- ==the headers of each frame are formatted for the specific medium that it will cross== 
- the media type determines the complexity of the frame 
	- the control info in the header and trailer of a frame depends on the transmission medium 
	- in a 'fragile environment' more controls are needed to ensure delivery 
		- therefore, header and trailer fields are larger to accommodate required control info == slower transmission rates 
			- because header has to account for more variables 
				- ex: weather, other signals, etc.. 
		- ex: 
			- wifi 
	- in 'protected environment' can count on the frame arriving at its destination 
		- fewer controls are needed
		- therefore smaller frame fields and smaller frames == faster transmission rates 
		- ex: 
			- physical Ethernet cables
- but regardless of the type of media used to connect devices, it is still referred to as a 'frame' 

#### Serial connection vs Ethernet connection
![[Pasted image 20250206132835.png]]
- serial 
	- only two devices connected (one at each end)
	- therefore, serial does not have to consider many different receivers 
		- easier to design
- ethernet
	- multiple devices connected
	- difference between hub and switch 
		- hub forwards everything 
		- switch uses MAC addresses to determine where to send 
	- has to account for multiple potential receivers
		- to differentiate, use Switch
		- this adds complexity, making it more complicated to design 

###### Examples
![[Pasted image 20250206154151.png]]

![[Pasted image 20250206154126.png]]

- ==Can see that Point-to-Point Frame header is much smaller than the Ethernet Frame header== 
	- because p2p has to account for very few variables because it is way less complicated 









## Formatting Data for Transmission
![[Pasted image 20250206143234.png]]
How to determine the start and stop of the frame?
- the 'Frame Start' and 'Frame Stop' fields
	- specific bit pattern denotes start of frame 
		- 1010
	- specific pattern denotes end of frame
		- 1001
- this is why different media have different frame design?

Addressing 
- used to determine source and destination MAC addresses 

Error Detection
- contains an error check value 
- sender calculates a value based on the data. this value is stored in this field 
- receiver recalculates value based on received data.
	- if not the same, indicates problem along path, missing data, interrupted connection etc..


## Wireshark Examples
![[Pasted image 20250206144058.png]]
- can see this is an Ethernet 2 frame
- highlighted is the 'header' 
- the Type line determines what the immediate upper layer is?

![[Pasted image 20250206144157.png]]
- this is an Ethernet 2.3 Frame 
- the highlighted thing is the 'header'


NOTE
- the type of header depends on the type of Frame (which depends on the media)
	- different types of headers will have different information so data can travel whatever media its designed for


In Wireshark 
- the order of PDUs is reversed 
	- first listed is lowest in the OSI model (ex: physical, network...  ..)
	- last listed is highest in OSI model (ex: application, ...)
- therefore, all things are considered 'payload' 
	- because everything encapsulated by upper levels and so on 
	- headers change and affect context of what's considered a payload
- everything except headers are considered payload?




# Data Link Standards
![[Pasted image 20250206144948.png]]
- vendors provide standards for their media 

# Controlling Access to the Media 
- protocols at the data link layer define the rules for access of the frames to different media #qc 
- WAN is a point-to-point sharing medium
- LAN is a shared medium 


## Physical and Logical Topologies 
### Physical Topology 
- includes: 
	- the actual layout of the floor/building
		- ex: rooms, corridors, doors
	- the location of devices based off the floor layout
		- what room are the main servers located, where are the switches and routers located, what rooms get networking devices and which ones do not, where are end devices located, where are wireless access points 
- does not include:
	- ip address assignment 
![[Pasted image 20250206151204.png]]


### Logical Topology 
- includes:
	- what devices exists in the network and how they are connected 
	- medium of connection 
		- therefore know media of communication and the Ethernet standards in use 
	- IP addresses 
	- know what protocols are used between devices 
- does not include
	- floorplan / room layout and where devices are located in relation 
![[Pasted image 20250206151233.png]]



## Common Physical WAN Topologies 
![[Pasted image 20250206152025.png]]
#### Point-to-point 
- example of 'serial connection' 
	- because only two devices connected in serial connection 
	- only one receiver (therefore no broadcast to all devices)
###### Logical Point-to-Point Connection
- high level topology
	- beyond scope of course
- a 'virtual circuit' is a logical connection created within a network between two point-to-point logically connected network devices 
	- can use to simulate a serial connection, even though realistically this is impossible 

###### Half and Full Duplex
![[Pasted image 20250206151841.png]]
- Half-duplex
	- the cable can only handle one-way traffic 
		- collision occurs if traffic is sent both ways 
	- therefore, once data is sent, must wait till it is received before sending another thing 
		- Ethernet establishes 'arbitration' to resolve signal conflicts for medium access control
- Full-duplex
	- cable can handle data being sent both ways 
	- no 'arbitration' necessary
	- these days, all cables are full-duplex 




## Common Physical LAN Topologies
![[Pasted image 20250206152105.png]]
Star topology
- easy to install, scalable, and easy to troubleshoot
- previously used to use hubs, now use switches

Bus topology 
- used in legacy Ethernet networks 
- bus needs to be terminated on both ends

Ring topology
- used in legacy Token Ring and Fiber Distributed Data Interface (FDDI) networks 
- each device connected between two neighbour devices forming a ring network 





## Important Example
![[Pasted image 20250206154524.png]]

As the traffic hops from one router to the next, the frame is decapsulated and then recapsulated again, depending on the media it has to traverse next 
- when first sent, the Frame is an 802.11 wireless frame, its then taken apart and encapsulted again, but as a PPP frame (point to point) etc... 

