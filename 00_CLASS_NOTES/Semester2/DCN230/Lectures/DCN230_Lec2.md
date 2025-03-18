---
class: 
topics: ipv4_subnetting
tags:
  - ipv4_subnetting
creation_date: 2025-01-16 08:06
last_updated: 2025-01-16 08:06
---
#### To Do
- [ ]
---
# DCN230_Lec2

# Slides
## Chapter 7 Slides:
![[Wk2_NB_Ch7_IPv4 Addressing.pdf]]

## Chapter 8 Slides:
![[Wk2_NB_Ch8_IPv4 Subnetting.pdf]]

# Number Conversion
## Converting from Binary to Decimal
Conversion table between binary and decimal is important to remember for subnetting 

| Position of bit | $2^7$ | $2^6$ | $2^5$ | $2^4$ | $2^3$ | $2^2$ | $2^1$ | $2^0$ |
| --------------- | ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| Decimal value   | 128   | 64    | 32    | 16    | 8     | 4     | 2     | 1     |

## Converting from Decimal to Binary 
![[Pasted image 20250116081946.png]]
#### Steps Involved:
- beginning with the largest octet binaryq value, compare the provided decimal value to see if its greater or smaller 
	- if greater, the big contributes 
		- therefore, take the decimal value and subtract the octet bit value 
		- set to 1
	- if smaller, no bit contributes 
		- set to 0
- continue from the largest octet bit value to smallest 
	- by the end you will have the complete binary value of the provided decimal value


# Subnetting 
## Network Portion vs Host Portion 
- subnet mask is required to differentiate between the network and host portions of the IP address 

![[Pasted image 20250116082513.png]]

### Network Portion 
- distinguishes what network the IP address is part of 
	- all devices on the same network have the same 'network portion' of their IP address 
- you know where the network portion ends and where the host portion begins based off the subnet mask
- if asked to find what network an Ip address belongs to, you are trying to find the network id based off the subnet mask 

### Host Portion 
- the specific device interface on the network 
- also known as 'member id' 

#### How to determine the number of available host addresses
- determine how many host bits there are 
	- 32 bits - 24 bits (subnet mask bits) = 8 bits (host)

## Legacy Classful Subnetting 

| Class | Subnet Mask   | Size of Network Portion Bit Field | Size of Host Portion Bit Field | Start Address | End Address     |
| ----- | ------------- | --------------------------------- | ------------------------------ | ------------- | --------------- |
| A     | 255.0.0.0     | 8                                 | 24                             | 0.0.0.0       | 127.255.255.255 |
| B     | 255.255.0.0.  | 16                                | 16                             | 128.0.0.0     | 191.255.255.255 |
| C     | 255.255.255.0 | 24                                | 8                              | 192.0.0.0     | 233.255.255.255 |

### About Classful Subnetting 
- Can determine what class an IP address belongs to based off the first 4 bits 



## Classless 



### Subnetting Examples
![[Pasted image 20250116084815.png]]




## Unicast, Broadcast, Multicast 
#### Unicast Transmission 

#### Broadcast 
- within same network 
#### Multicast 
- there is a defined range used for multicast messages 
- therefore if you want to send a multicast message, must select an IP address from list 
#### Private IP addresses
- these addresses can only be recognized by devices within the same network (same network ID)


## Reserved IP addresses 





## Legacy Classful Addressing 

| Address Class | 1st Octect Range | Network(N) and  |
| ------------- | ---------------- | --------------- |
|               |                  |                 |

## Why Classless is Now Used
- supports more users
- if still using Legacy Classful Addressing, would quickly run out of IP addresses 
	- because subnet masks are fixed 


# Subnetting Tips
- to calculate the first and last available host IPs, it is much easier to convert into binary