---
class: dcn230
topics: 
tags:
  - vlan
  - trunking
creation_date: 2025-02-25 20:18
last_updated: 2025-02-25 20:18
---
#### To Do
- [ ]
---
# DCN230_Lec7
# VLANs and Trunking
![[Wk8_VLANs and Trunks.pdf]]



even if part of same subnet, if separated into vlans cannot communicate between vlans, even though still part of the same subnet 
- layer 3 part of same subnet
- layer 2 part of different vlans 
- UNLESS you incorporate something else, then they can communicate
	- but this will be covered later

vlan1
- default vlan 
- cannot be deleted
- default management vlan
- NOTE
	- can assign these default features to other vlans 


Trunking
- makes multiple VLAN traffic possible across different switches 
- allows multiple VLANs to be handled by a single physical link 
	- only networking devices use trunking 
		- regular end devices connect via access ports, NOT trunks 
	- ex: switch to switch 


# VLANs
---
### What is a VLAN



### Benefits of VLAN design 
- smaller broadcast domains 
	- dividing LAN reduces number of broadcast domains 
- improve security 
	- only users in same VLAN can communicate together 
- improve IT efficiency 
	- VLANs can group devices with similar requirements 
	- ex: faculty vs students 
- reduced cost
	- one switch can support multiple groups or VLANs
- better performance 
	- small broadcast domains reduce traffic, improving bandwidth 
- simpler management 
	- similar groups will need similar applications and other network resources 
	- ex: printing, VoIP etc.. 



## VLAN Tagging
---
- VLAN tags are applied by the switch 
	- NOT end device sending the data
- switch knows what VLAN tag to apply based on what port the data was received 
	- based on the port's VLAN assignment 
- IF end device is a router, sw



## Special VLANs 
---
#### VLAN 1
![[Pasted image 20250305172857.png]]
- VLAN 1 is the following:
	- default VLAN 
	- default Native VLAN 
	- default Management VLAN
	- CANNOT be deleted or renamed
- recommended to assign these default features to other VLANs 


#### **Native VLAN** 
- [[Native VLAN]] 
- no VLAN tag is applied for Native VLAN 
- if a port is assigned as part of the Native VLAN, traffic received on that port will NOT receive a VLAN tag
- ==only 1 Native VLAN per trunk port== 
	- by default it is vlan1, but you can assign any custom vlan as the native vlan 
	- even if a switch has multiple trunk ports, each trunk port can have a different Native VLAN 
- ==Native VLAN is local specific!! ==
	- therefore, always try to ensure both side of the trunk link use the same Native VLAN to avoid VLAN mismatch
	- if sending switch has different native vlan than receiving switch, can cause serious problems
		- can lead to [[VLAN Hopping]] 
		- ex: say there are 2 devices (A and B)
			- A is the sending device and has native vlan 99
			- B is the receiving device and has native vlan 1
			- step 1:
				- device on vlan 99 sends untagged frame to switch A
				- switch A sends the frame through the trunk port untagged to switch B
			- step 2:
				- switch B receives the untagged frame through the trunk port 
				- since switch B has native vlan 1, it treats the untagged frame as vlan 1 traffic 
					- can cause serious problems 
					- may send traffic out the wrong interface 
- configuration 
	- trunk port is the interface where you configure the Native VLAN 
		- because the Native VLAN only applies to trunk ports, not access ports 
	- although you can configure a no trunk port to the native vlan, it will still leave the switch with no vlan tag
- NOTE:
	- the native VLAN is important when configuring a trunk port and trunk connection 
		- every trunk port must have a native vlan configured
		- will still work if no native vlan for the trunk port was configured 
			- BUT the native vlan will be the default vlan 1
				- not good for security reasons ([[VLAN Hopping]]) 






## VLAN Ranges on Catalyst Switches
---
- Catalyst switches 2960 and 3650 support over 4000 VLANs

| Normal Range VLAN 1 - 1005                           | Extended Range VLAN 1006 - 4095 |
| ---------------------------------------------------- | ------------------------------- |
| used in small to medium sized businesses             | used by service providers       |
| 1002 - 1005 are reserved for legacy VLANs            | are running-config              |
| 1, 1002 -1005 are auto created and cannot be deleted | supports fewer VLAN features    |
| store in the vlan.dat file in flash                  | requires VTP configurations     |
| VTP can synchronize between switches                 |                                 |
NOTE
- vlan.dat 
	- deleting vlan database will remove the entire vlan plan 






## Types of VLANs 
---
#### Data VLAN
- dedicated to user-generated traffic 
	- ex: email and web traffic 
- VLAN 1 is the default data VLAN 
	- all interfaces are assigned to this VLAN by default 

#### Native VLAN
- used for trunk links only
- all frames are tagged on an 802.1Q trunk link
	- except for those on the native VLAN 
- for legacy use
	- ex: hub 
		- because hub cannot recognize vlans 
		- traffic from hub would be considered part of native vlan


#### Management VLAN 
- mainly used for management purposes 
	- but apart from this, not really different from regular 
- mainly used for SSH/Telnet VTY traffic and should not be carried with end user traffic 
	- ex: a VLAN dedicated for remote connections for Admins 
- usually vlan1 is the default management vlan
	- but can change this by moving the switch IP address to the desired vlan 
- NOTE:
	- requires an IP address to be assigned to the management vlan 
		- [[DCN230_Lab6]] part 2 step 2.2

###### Example from Lab6 to move the Management vlan from VLAN 1 to VLAN 99
```C++
S1(config)# interface vlan 1
S1(config-if)# no ip address
S1(config-if)# interface vlan 99
S1(config-if)# ip address 192.168.1.11 255.255.255.0
S1(config-if)# end
```
- NOTE:
	- this removes the IP address from vlan 1 then applies it to vlan 99 which makes vlan 99 the new management vlan 





#### Voice VLAN 
- a separate VLAN that can support Voice 
	- in addition to regular computer data traffic
- requires separate VLAN for voice because:
	- assures bandwidth
	- high QoS priority
		- voice is prioritized over other network traffic 
	- ability to avoid congestion 
	- delay less than 150 ms from source to destination 
- entire network must be designed to support voice 

![[Pasted image 20250305175203.png]]
- from this example, how does the switch know it received voice traffic vs regular data traffic?
	- because the computer and phone are assigned to different VLANs 
	- when the phone sends UDP data, it has the 'phone VLAN tag' 
		- therefore, switch will know to prioritize this traffic 
	- when the computer sends TCP (reliable), data it has the 'computer VLAN tag' 
- think of this phone as a smaller 'switch' 
	- when user inputs voice data, it is automatically tagged as 'phone VLAN' 
	- when phone receives data from computer via the port, it is tagged as 'computer VLAN' 
	- the switch does the tagging? #question 
		- or the computer 








# Trunks
---
### What is a Trunk
- a point-to-point link between two network devices 
- purpose: 
	- allows more traffic from multiple VLANs on a single interface 


---


### Dynamic Trunking Protocol 
- DTP is a proprietary Cisco protocol 

| Option (port modes)    | Description                                                                                                                                             |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| access                 | permanent access mode and negotiates to convert the neighbouring link into an access link                                                               |
| dynamic auto (default) | will become a trunk interface if the neighbouring interface is set to trunk or desirable mode                                                           |
| dynamic desireable     | actively seeks to become a trunk by negotiating with other auto or desirable interfaces                                                                 |
| trunk                  | permanent trunking mode and negotiates to convert the neighbouring link into a trunk link (allows for control over what VLANs are allowed on the trunk) |
- NOTE:
	- dynamic-auto is the default option for Cisco Catalyst 2960 and 2950 switches 
	- both `trunk` and `access` require manual configuration 

###### Chart that describes the type of trunk based on the configuration for both interfaces 
![[Pasted image 20250306094340.png]]
- left column = interface on one side of the trunk 
- top row = interface on the other side of the trunk
- Access
	- means the connection is an 'access' connection
	- can only support 1 vlan 
	- no vlan tags added to frame 
- Trunk
	- connection is a 'trunk' connection
	- can support multiple vlans
- Limited connectivity 
	- one side is 'trunk' and the other is 'access port' 
	- only native vlan will work 
		- because 'access port' side can only handle one vlan 
		- access port side dropps tagged frames 
	- recall that access ports drop incoming tagged frames 
	- best practice: avoid limited connectivity



---


### Trunking White List
- can specify what vlans are allowed through the trunk link 


---


### VLAN Tagging
![[Pasted image 20250305191328.png]]
- tagging allows switch to know what vlan data belongs to 
- switch knows what vlan data belongs to based on what port it receives the data 
- NOTE:
	- the VID field is the most important for vlan tagging (from above slide screenshot)
- using WireShark:
	- can see the vlan tag
	- ![[Pasted image 20250305191528.png]]
		 - can see this frame was tagged as vlan 10


#### ==Tagging Rules==
1.  frames arriving/leaving an access port are always untagged 
	- if an access port receives a tagged frame, it drops it 
2. an untagged frame arriving at an access port is assumed to belong to the VLAN associated with that port 
3. a switch forwards frames from a VLAN only to the access ports within that VLAN and trunk ports which allow that VLAN
4. a trunk port forwards frames from a VLAN which is the same as its native VLAN, untagged
5. an untagged frame arriving at a trunk port will be forwarded to the native VLAN which is configured on the trunk 



---





# Configuration 
---
### To create a VLAN 
```C++
# VLAN creation done in global conf mode 
switch# conf t
# To create a VLAN with valid ID number
switch(config)# vlan <vlan-id>
# Specify a unique name to identify the VLAN (optional)
switch(config-vlan)# name <vlan-name>
# Return to the privileged EXEC mode
switch(config-vlan)# end
```
- NOTE
	- vlans are created in ==global config mode==
	- vlan details stored in vlan.dat file 
	- do NOT have to create vlan first before assigning ports to it 
		- if the vlan does not exist when assigning an interface to it, the vlan is created automatically and added to database 


---


### VLAN port assignment 
```C++
# Enter global conf mode
switch# conf t
# Enter interface config mode
switch(config)# interface <interface-id>
# Set the port to access mode
switch(config-if)# switchport mode access
# Assign the port to a VLAN
switch(config-if)# switchport access vlan <vlan-id>
# Return to privileged EXEC mode
switch(config-if)# end
```
- NOTE
	- `switchport mode access` configures the interface as an 'access interface' 
		- access interface = interfaced used to connect to end devices 
	- can assign a vlan to multiple interfaces at the same time 
		- see: [[DCN230_Lab6]] Part3, step 1a 


###### To assign a VLAN to a single port:
```C++
S1(config)# interface f0/6
S1(config-if)# switchport mode access
S1(config-if)# switchport access vlan 10
```
- `access` mode means the port only handles traffic for 1 vlan 



###### To assign a VLAN to multiple ports at same time:
```C++
S1(config)# interface range f0/11-24
S1(config-if-range)# switchport mode access
S1(config-if-range)# switchport access vlan 10
S1(config-if-range)# end
```
- assigns interfaces from f0/11 - 24 to vlan 10


---
### Configure Management VLAN
###### Example from Lab6 to move the Management vlan from VLAN 1 to VLAN 99
```C++
S1(config)# interface vlan 1
S1(config-if)# no ip address
S1(config-if)# interface vlan 99
S1(config-if)# ip address 192.168.1.11 255.255.255.0
S1(config-if)# end
```
- NOTE:
	- this removes the IP address from vlan 1 then applies it to vlan 99 which makes vlan 99 the new management vlan 


---



### To delete a VLAN
```C++
# Enter global conf mode
switch# conf t
switch(config)# no vlan <vlan-id>
switch(config)# end
```
- NOTE
	- ==always reassign all ports associated with a vlan before deleting the vlan!==
		- what happens if ports are not reassigned before deleting their associated vlan?
			- interface formally remains in associated access vlan, BUT all received traffic is dropped on arrival 
			- the interface will no longer appear when `show vlan brief` is executed 
				- disappears 




---



### Trunk Configuration 
```C++
# Enter global conf mode
switch# conf t
# Enter interface config mode
switch(config)# interface <interface-id>
# Set the port to permanent TRUNK mode
# switchport mode has additional options listed below
switch(config-if)# switchport mode trunk
# Set the native VLAN as something other than VLAN 1
switch(config-if)# switchport trunk native vlan <vlan-id>
# Specify the lsit of VLANs to  be allowed on the trunk link
switch(config-if)# switchport trunk allowed vlan <vlan-list>
# Return to privileged EXEC mode
switch(config-if)# end
```
- NOTE:
	- additional options for `switchport mode` command:

#### Dynamic Trunking Protocol 
```C++
# DTP configs done through the 'switchport mode' command
# Use 'switchport nonegotiate' command to disable DTP
#
# ACCESS option
switch(config-if)# switchport mode access
# DYNAMIC AUTO
switch(config-if)# switchport mode dynamic auto 
# DYNAMIC DESIREABLE
switch(config-if)# switchport mode dynamic desireable
# TRUNK
switch(config-if)# switchport mode trunk
```














---























# Final Notes
---
- its possible to handle traffic for multiple VLANs between two switches WITHOUT trunking BUT this requires separate connections for each VLAN 
	- if there are many vlans, this would be highly inefficient because it would require an unrealistic amount of ports on a switch to dedicate to individual vlans 
	- okay for smaller networks
	- not scale-able tho for larger networks 







