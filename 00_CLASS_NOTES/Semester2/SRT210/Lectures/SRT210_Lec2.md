---
class: srt210
topics: 
tags:
  - nftables
  - network_security
creation_date: 2025-01-20 08:05
last_updated: 2025-01-20 08:05
---
#### To Do
- [ ]
---
# SRT210_Lec2

## Admin
- labs open Monday midnight
	- open lab will be open 
	- lab3 due next Monday 
- Tuesday will be when the new topic is introduced 

## Lecture Slides:
![[NFTables.pdf]]


### Gather Info
- always first step 
- 1. check if nftables is the default firewall
	- ubuntu is ufw by default? 
		- to check: systemctl status ufw
	- to check firewalld
		- sudo systemctl status firewalld
	- to check nftables:
		- sudo systemctl status nftables 
		- NOTE
			- has to be enabled and active
				- to do both at the same time:
					- sudo systemctl enable --now nftables 
			- might not be by default
	- firewall choice is often dictated by the organization (organizational policies)
- 2. check the current rules 
	- sudo nft list ruleset

## Working with Tables
- firwalld is not similar to iptables 
- incoming and outgoing traffic passes through several tables and chains, depending on the type of traffic 
	- not all traffic interacts with all tables 
- main focus for this course
	- filter table 
- there are default tables BUT they don't do anything 
- NOTE
	- might see the same chain name in different tables, and both mean different things 
	- a table is mainly a 'placeholder' 
		- just a way of organizing rules 
		- really need chains to actually do things 


### Adding/Deleting Tables
- sudo nft add table \<family> \<name>
- sudo nft delete table \<family> \<name>

## Working with Families 
- can have one rule apply to multiple families of traffic 
	- does not work with iptables 
- can setup separate tables based on family 
- ex:
	- inet = both ipv4 and ipv6
	- ip = ipv4

## Chain Types 
- each chains need a 'type' and each type needs a 'hook'
	- hooks are used to connect your rules to actual traffic 
	- if you don't connect to the correct hook, rules will not apply

### Important Hooks
- there are LOTs
- we will focus on:
	- input 
		- when traffic shows up at my machine, what would I like to do with it
	- output 
		- what to do with traffic being sent from my machine
	- forward
		- traffic arriving on this machine, addressed somewhere else 
		- ex: our Gateway machine 


### Creating Chains
- \<table name>
	- name of the table this chain is added to 
- \<chain name>
	- best practice, name it after the hook it will be attached to 
- \<type>
- \<policy>
	- accept is default, but better to be drop
- \<priority>
	- // determines the priority of CHAIN
	- within chain, priority of rules depends on the order of rules 

## Policies
- usually every chain will have one or more rules that describe the kind of traffic you want through the chain 
- if there is a packet that matches with the rule
	- can: allow, block, send to a different place etc.. 
- if the packet does not match, it gets passed to the next rule etc..
- if the packet does not match with any rules, the 'policy' is applied 
	- allow (default), ==block== (better choice)
	- 'policy' is like a catch all statement 
## Rule Format
- \<table type>
- \<table name>
- \<chain name>
- \<filters>
	- the actual stuff you are checking for in the packets 
	- if set to 0, will match with all traffic 
	- lots of filtering options
- \<target>
	- what to do with packets that match this rule 

### Filters in Rules 
- protocol
	- ex: tcp 
- source address
	- who is sending the packet
	- can be: IP, 
		- a range of IPs, names, etc.. 
	- ex: ip saddr \<single IP or network ID or range of IP addresses> 
- destination address
	- ex: ip daddr 
- source port
	- what port is this being sent from from the sending machine 
	- for port filtering, have to specify a protocol 
	- can list a single port by number: tcp dport 22
	- can give a range of ports 37-39
	- can give a list of ports {80,443}
		- better to create single rules for each port 
		- in case have to remove a port or change a rule for a specific port 
- iifname \<name of interface>
	- incoming interface
	- MUST make sure everything for loopback address is allowed
- oifname \<name of interface>
	- outgoing interface 
	- MUST make sure everything for loopback address is allowed
		- ex: oifname lo accept 
			- // lo is loopback
- NOTE


## Filter Order 
- within a rule, the order of filtering rules does not matter 
- BUT the order of rules themselves DOES matter
	- rules at the top of chain are first to get checked
		- once a rule matches with a rule, it is done with the chain 
- to add a rule to the top of chain
	- sudo nft insert rule ...
- to add a rule to the end of a chain 
	- sudo nft add rule ...
- can add rule at a specific placement by using the position 
	- but need to know the positional placement of all other rules to effectively do this 
	- 'handle' 
	- to see the handle numbers execute:
		- sudo nft list ruleset -a
		- ==NOTE==
			- handle numbers for remaining rules DOES NOT CHANGE after you delete a rule at a specific handle location 

#### Rule Counter
- good way to test if a rule is actually working 
- not required all the time, but good when setting up 
- can also see how frequently a specific service is being used 
- ex: 
	- add counter to the end of a rule 
	- sudo nft insert rule into inet filter input tcp dport 9090 counter accept 
		- // target has to be the last thing 
- when you execute sudo nft list ruleset, will display all the packets that matches a rule 

## Ensuring Boot Persistence
- Usually you want your rules to apply automatically upon boot 
	- usually want this to be the case for all configurations for your machine 
- Several ways to make nftables apply every time machine boots
	- ops105 we used a script 
	- /etc/nftables.conf
		- nftables automatically 
		- better

#### /etc/nftables.conf
- this config file needs:
	- needs shebang line at top 
		- ==#!/usr/sbin/nft -f==
			- // but be at the top of the /etc/nftables.conf file 
	- Also need to flush the ruleset
		- ==flush ruleset== 
			- must be after the shebang line 
		- without this, nftables may continue to add rules on top of rules with every reboot 
			- will not cause rules to break, but hard to manage and clutters config file 
		- need to flush 
- once the shebang line and flush ruleset line have been added, can add all rules directly to this file 

## Example
- although it is not a requirement to apply rules to both incoming and outgoing connection, it is a good idea to do so 
- inet main input ct state related, established accept 
	- // might see this as a result, because the response traffic is accepted by default 
	- but what happens if the server is compromised? 












