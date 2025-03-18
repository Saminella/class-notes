---
class: 
topics: 
tags: 
creation_date: 2025-03-17 09:22
last_updated: 2025-03-17 09:22
---
#### To Do
- [ ]
---
# DCN230_Lec8
## Slides
![[Wk9_InterVLAN Routing.pdf]]



# Inter-VLAN Routing 
---
### What is Inter-VLAN Routing
- VLANs are used to segment switched Layer 2 networks 
- hosts in one VLAN cannot communicates with hosts in another VLAN 
	- unless there is a router or Layer 3 switch to provide routing services 


##### If machines are in the same subnet but different VLANs they CANNOT communicate
- even if in the same subnet, still separated at layer 2





# 3 Inter-VLAN Routing Options 
---
#### Legacy Inter-VLAN Routing
- legacy solution
- does not scale well
	- every VLAN requires its own dedicated port 
	- if lots of VLANs, will get expensive and difficult to manage 
- NOTE:
	- this method of inter-VLAN routing is no longer implemented in switched networks and is included for explanation purposes only

---

#### **Router-on-a-Stick** (focus for today)
- acceptable solution for small to medium-sized networks
- involves configuring subinterfaces on a trunked port on a router 
	- each sub interface connected to a VLAN 
	- max subnets: 50 VLANs


---

#### Layer 3 Switch using Switched Virtual Interfaces (SVIs) 
- most scalable solution 
- for medium to large organizations 
- switch interface becomes a 'router interface'
	- becomes a 'layer 3 router interface'
- NOTE:
	- beyond scope of this course 
	- but called SVIs (switched virtual interfaces) if interested



# More about Router on a Stick 
![[Pasted image 20250318115557.png]]

![[Pasted image 20250318132748.png]]
- how traffic flows from one network to the other
- NOTE
	- possible to replace R1 with a Layer 3 switch 


### For Router-on-a-stick to work, must configure 'subinterfaces'
- must create subinterfaces for each VLAN 
- then assign IP addresses for each of the subinterfaces 
	- these will act as the default gateway IPs for each VLAN

#### How to configure subinterfaces
```C+
R1(config)# interface G0/0/1.10
R1(config-subif)# Description Default Gateway for VLAN 10
R1(config-subif)# encapsulation dot1Q 10
R1(config-subif)# ip add 192.168.10.1 255.255.255.0
R1(config-subif)# exit
R1(config)# interface G0/0/1.20
R1(config-subif)# Description Default Gateway for VLAN 20 
R1(config-subif)# encapsulation dot1Q 20
R1(config-subif)# ip add 192.168.20.1 255.255.255.0
R1(config-subif)# exit
R1(config)# interface G0/0/1
R1(config-subif)# Description Trunk link to S1
R1(config-subif)# no shut  # IMPORTANT 
R1(config-subif)# end
```
NOTE
- G0/0/1 is the main interface 
	- this is broken into multiple sub interfaces
	- MUST set main interface to up
		- else all sub interfaces will be down 










# Troubleshooting Common Inter-VLAN Issues
---
- check physical connection is correct 
- missing VLANs
- switchport trunk port issues
- switch access port issues
- router configuration issues


![[Pasted image 20250318133257.png]]

















