---
class: 
topics: 
tags: 
creation_date: 2025-02-14 19:32
last_updated: 2025-02-14 19:32
---
#### To Do
- [ ]
---
# DCN230_Lec6
# Important Protocols 

![[Wk6_Part1_NB_Important Protocols.pdf]]

![[Wk6_Part2_DHCPv4.pdf]]

# Important Protocols 
## Goals
---
- this lecture will focus on [[Application Layer]] protocols 
- DNS
- DHCP
- FTP
- TCP and UDP
- IP










# ==Application Layer Protocols== 
# DNS
---
### Purpose
- associates domain names with various information, especially the IP address 
- required for IP protocol 
	- ex: to get the destination IP for an HTTP packet, have to query the DNS server for the IP address associated with the domain name. Then, can use this addresses for the destination field in HTTP packet 


### DNS Hierarchy 
![[Pasted image 20250215135107.png]]
- root DNS 
- Top Level Domain (TLD)
	- 13 total, distributed across the globe
	- multiple mirrors for each 
	- contain top level server IP addresses 
		- where 'recursive' queries are sent 





# DHCP
---
### Purpose
- allows a host to obtain an IP address dynamically 
	- includes: IP addresses, subnet masks, gateway, and other networking parameters 
- DHCP server is contacted and address requested 
	- chooses address from pool and 'leases' it to host for set period 


### DORA
![[Pasted image 20250215140002.png]]

#### Initial steps involved in DHCP allocation (DORA)
- DHCP Discover
	- broadcast message 
- DHCP Offer
	- if multiple DHCP server, will get multiple offers for IP addresses
		- client chooses 1, and sends in request message 
- DHCP Request
- DHCP Ack

### Steps to renew a lease
![[Pasted image 20250215140112.png]]




# FTP
---
### Purpose
- allows data transfer between a client and server 
- [[Application Layer]] protocol that uses [[Transmission Control Protocol|TCP]] 
- what is an FTP 'client'?
	- an application that runs on a computer that is used to push and pull data from a server running an FTP daemon 

### How it works
- FTP requires two connections (TCP) between the client and server:
	- one for commands and replies (port 21 on server)
		- ex: command to 'download' / 'upload' 
	- one for the actual file transfer (port 2 on server)

![[Pasted image 20250215140529.png]]










# ==Transport Layer Protocols== 
### Role of the [[Transport Layer]] 
- responsible for establishing a temporary communication session between two applications and delivering data between them 
- primary responsibilities include:
	- tracking the individual communication between applications on the source and destination hosts
	- segmenting data for manageability and reassembling segmented data into streams of application data at the destination 
		- only tcp supports segmentation 
	- identifying the proper application for each communication stream 
- TCP/IP uses 2 protocols to achieve this
	- Transmission Control Protocol (TCP)
		- for applications that require reliability 
	- User Datagram Protocol (UDP)
		- for applications that don't require reliability and which prioritize speed / efficiency 

![[Pasted image 20250215143246.png]]






# TCP
---
### Purpose
- transfers data over the internet RELIABLY
- http://networkstatic.net/what-are-ethernet-ip-and-tcp-headers-in-wireshark-captures/
	- very helpful for understanding TCP wireshark captures
- allows for 'segmentation' 
	- therefore, header contains 'Sequence Number' and 'Payload number' 
		- UDP does not because does not support segmentation 
	- sequence number required to reassemble segmented data
- why is segmentation important for TCP?
	- because if you have to send a big thing of data without segmenting it and something goes wrong in the transmission, you lose the entire thing of data and have to resend everything which would take a lot of time 
		- therefore, segmentation enhances reliability and reduced bandwidth consumption 
	- to accommodate limitations of lower level protocols 
		- ex: Ethernet has min and max size 


### Drawbacks
- much larger overhead (demanding) due to reliability requirement 
	- to allow for reliability, header is much larger (sequence number, ack num, flags etc..)


### How TCP works
- TCP breaks down the message delivered from the application layer into a number of numbered segments 
- TCP tracks the numbered segments sent to a specific receiver application from a specific sender application 
- TCP re-sends the segments that are NOT acknowledged within a defined period of time 
- TCP at the receiving host reassembles the message from the received segments and passes them up to the application layer 
- NOTE:
	- the process of placing acknowledgements, tracking and retransmission, result in more overhead in the network 











### TCP header
![[Pasted image 20250215143354.png]]

![[Pasted image 20250215143531.png]]

ABOUT
- Sequence number 
	- informs receiver where the data in that segment starts from witin the stream of bytes 










#### TCP Example
##### Example of HTTP 'Continuation' packet 
![[Pasted image 20250215145549.png]]

- HTTP uses TCP
- a 'continuation' packet is what is produced when data must be segmented 
	- its a continuation of the same 'stream' of data
- the 'Sequence Number' means first byte of this segment is the 1461th byte in the stream 
	- can see that the TCP Segment Len: 1460
		- therefore, we know this is the second packet in this data stream 
- the line below 'Sequence Number (raw)' is the randomly chosen sequence number chosen by TCP 
	- for security purposes 
	- [[TCP Sequence Prediction Attack]] 
		- a much older attack, not common these days 
- the line '\[Next Sequence Number: 2921]' 
	- indicates what the next sequence number will be 
	- in this case, since we know the sequence number for this packet is 1461 and the TCP segment length is 1460, we know the next sequence number will be 2921 (1460 + 14161)
- 'Acknowledgement Number: 325'
	- even though it is not an ACK message, still needs acknowledgement number 


##### The TCP ACK reply to the above 'Continuation' message 
![[Pasted image 20250215151118.png]]

- Acknowledgement Number : 2921
	- sent by receiver in an ACK message to tell the sender which byte it expects in the next packet 
	- if packets are LOST the ACK number stays the same 
		- receiver keeps asking for the missing bytes 









# UDP
---
### Purpose
- transfers data over the internet QUICKLY 
	- 'best effort'





### UDP header 




