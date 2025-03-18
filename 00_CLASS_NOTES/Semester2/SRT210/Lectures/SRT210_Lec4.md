---
class: srt210
topics: dns
tags:
  - dns
  - linux_networking
creation_date: 2025-02-04 11:30
last_updated: 2025-02-04 11:30
---
#### To Do
- [ ]
---
# SRT210_Lec4
# DNS
# DNS Refresher
---
### Forward mapping 
- translates between human-readable names and machine-useful IP addresses 
- can do this with a static file /etc/hosts
	- BUT would need one file for each machine and would need to edit all of them every time anything changed 
	- although could work locally for small networks internet could NOT function like this
### Reverse mapping 
- translates from and IP address to a human-readable name 

## Purpose of DNS
- a hierarchal distributed database that divides machines up into zones 
	- similar to domains
- when you're trying to find a machine, you ask a name server for that zone 
	- don't know where the name server is?
		- as the name server for its 'zone' 
	- ex: trying to find matrix.seneca.on.ca

### Root name servers
- where you go if you don't know anything 
- required for all this to work 
- in known locations (and don't change) and know the locations of the name servers for the top level domains 




---




# Terminology 
---
### Zone vs Domain 
- domain
	- a named section of a network (ex: on.ca) and everything inside (below) it (ex: seneca.on.ca)
- zone 
	- the domain, without any of its sub-domains 
	- ex: just on.ca, not including seneca or anything else 
- name servers are responsible usually for a zone 
	- they should know about the other name servers that represent zones in sub-domains 
		- but not the other machines 
		- this is what creates the hierarchical structure 


### Primary and Secondary (Master and Slave) 
- Primary server
	- holds a copy of the answers it can provide on the local disk 
		- has the actual zone files in local storage 
	- an admin can manually change these 
- Secondary 
	- gets copies of the data from the primary 
		- through process called 'zone transfer' 
	- must NOT be manually changed 
- together, the load is spread across multiple machines and protects in case on fails 
- any zone must have at least ONE primary and should have more than one secondary 
	- one of which should be off-side 




---




# Installation and Configuration 
---
### Installation 
- we will be using a service called 'named' (name-dee)
	- the package to install can vary between distributions, but is typically called either 'bind' or 'bind9' 
- on Ubuntu we will use bind9

### Configuration
- most configurations will take place in named.conf 
- some distributions store this as one big file, some split it into several smaller ones 
	- Ubuntu stores it in /etc/bind, and splits it into several files 
		- we are mainly interested in named.conf.options and named.conf.local 

##### named.conf.options (global)
- contains configuration parameters that control how the server will behave 
- ex: who it will respond to, what kind of responses it can give, what other servers it will talk to 
- https://bind9.readthedocs.io/en/v9.20.5/manpages.html#named-conf-configuration-file-for-named 

##### named.conf.local 
- where you define your internal zones 
	- ex: forward lookup and reverse lookup 

https://bind9.readthedocs.io/en/v9.18.14/chapter3.html
- helpful for understanding config files 
- different types of 'blocks'
	- ex: option block (global), zone block (specific zone), logging block



## Resource Records 
---
#### Most common resource records 
==SOA==
- 'Start of Authority' record
	- important record in a DNS zone file
- defines the primary source of information for the domain 
	- also contains metadata about the zone 
- see below for examples

==A==
- address
- describes a forward mapping entry 
- syntax:
```
<hostname> <TTL> <type> A <address>
matrix.senecac.on.ca. 2W IN A 142.204.140.90
```



==PRT==
- pointer 



==NS==
- 'name server' 
- NS records identify the name servers in this zone 
- syntax: 
```
<zone> <TTL> IN NS <hostname> 
senecac.on.ca. 1w IN NS ns1.senecac.on.ca
```
- NOTE:
	- if \<zone> is omitted, uses the same zone as the resource record preceding it 
	- the NS resource record is often listen immediately after the SOA record
		- therefore, usually does not include ZONE and TTL 

==TXT==
- text record



==MX==
- Mail eXchange 





### Forward Lookup Zone File **Examples**
---
#### /etc/bind/db.example.com (bind9 default template)
```
;
; BIND data file for example.com
;
$TTL    604800
@       IN      SOA     ns.example.com. root.example.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL

@       IN      NS      ns.example.com.
@       IN      A       192.168.1.10
@       IN      AAAA    ::1
ns      IN      A       192.168.1.10
```

for template:
```bash
sudo cp /etc/bind/db.local /etc/bind/db.example.com
```
- replace localhost with the FQDN of your server 
	- including the addition . at the end 
- change 127.0.0.1 to the nameserver's IP 
- change root.localhost to valid email address 
	- with a . instead of @
	- include the . at the end 

ABOUT
```
@       IN      SOA     ns.example.com. root.example.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
```
- 'Start of Authority' (SOA) record 
- @
	- refers to the domain itself (example.com) 
- IN
	- 'Internet Class'
	- tells Bind this is an Internet record 
- ns.example.com 
	- the 'primary name server' 
		- primary authoritative DNS server for the domain 
	- should be the same as the `@       IN      NS      ns.example.com.` line
- root.example.com.
	- email address of person responsible for domain 
	- @ is replaced with . 
- Serial number 2
	- version number for the zone file 
	- simpler way of tracking compared to date-based
		- but less history tracking 
- Refresh 
	- refresh interval in sec
		- 604800 sec == 7 days 
	- tells secondary DNS servers how often to check for updates 
- Retry 
	- retry interval 
		- 86400 sec == 1 day 
	- if secondary server fails to contact primary, will retry after this period
- Expire
	- if secondary DNS server cannot contact primary for this long, it stops serving the zone
- Negative cache TTL
	- controls how long a DNS resolver should cache a 'non-existent domain' (NXDOMAIN) response

```
@       IN      NS      ns.example.com.
```
- @ 
	- used for shorthand for the domain name (example.com)
	- same as `example.com.`
- NS
	- defines the authoritative name server for the domain (example.com) 

```
@       IN      A       192.168.1.10
```
- purpose:
	- the domain itself (example.com) resolves to 192.168.1.10
	- if users type example.com, it will resolve to 192.168.1.10
- **only required if domain should point to a web server**

```
@       IN      AAAA    ::1
```
- for IPv6 mapping (not required for srt210) 
	- means the domain can be resolved using IPv6
- IN
	- Internet class
	- same as for A records
- AAAA
	- means its an IPv6 address record 
- ::1
	- loopback address in IPv6
	- equivalent to 127.0.0.1 in IPv4

```
ns      IN      A       192.168.1.10
```
- purpose
	- resolves ns.example.com. to an IP 
	- required for name server to be reachable
#### From class
![[Pasted image 20250208110349.png]]
- explicitly declared each hostname with its domain 
	- instead of example above which uses @ as shorthand for the domain name

ABOUT
```
@ IN SOA ns.210.ops. hostmaster.210.ops 2025020301 1d 1h 1w 1h
```
- ns.210.ops.
	- primary DNS server FQDN
	- should be same as `IN NS ns.210.ops.` line 
- hostmaster.210.ops.
	- email
- 2025020301
	- serial number
	- follows the YYYYMMDDNN format (date based serial number) 
		- 2025 - year
		- 02 - month
		- 03 - day
		- 01 - revision number (first change of the day) 
		- easier to track changes 
- 1d
	- refresh 
- 1h
	- retry
- 1w
	- expire
- 1h
	- negative cache 

```
ub1.210.ops. IN A 192.168.157.10
ub2.210.ops. IN A 192.168.157.15
```
- explicitly declares each hostname with its domain 
	- ubu1 (hostname) .210.ops. (domain)
- could achieve same result by just specifying the hostname 
	- ex: `ubu1 IN A 192.168.157.10`




#### From spr100 A3
![[Pasted image 20250208112653.png]]









### Reverse Lookup Zone File **Examples**
---

#### /etc/bind/db.192 (reverse lookup)
```
;
; BIND reverse data file for local 192.168.1.XXX net
;
$TTL    604800
@       IN      SOA     ns.example.com. root.example.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      ns.
10      IN      PTR     ns.example.com.
```

for template:
```bash
sudo cp /etc/bind/db.127 /etc/bind/db.192
```





#### From class
![[Pasted image 20250208155557.png]]



#### From spr100 A3
![[Pasted image 20241130203804.png]]




















# Troubleshooting 
## Tools
### dig


### nslookup
- nslookup uses the DNS server set in /etc/resolv.conf


### tcpdump 




