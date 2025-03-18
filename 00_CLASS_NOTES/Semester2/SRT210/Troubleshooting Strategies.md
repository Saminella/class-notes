
# Steps
##### 1. Gather info 

##### 2. Narrow down what the potential problem is 
- check the obvious 

##### 3. Fully understand the mechanics for the most likely problems 

##### 4. Try solutions 


# Tools 
### tcpdump 
- very helpful for analyzing network traffic in CLI environment 
- can indicate
	- if traffic is making it to a server 
	- if traffic is being denied
	- if ports are receiving traffic 
	- what interfaces are receiving and sending traffic 
- how this helps
	- isolate what machine is the problem 
	- isolate what network variable(s) is the problem 

```bash
tcpdump -i port 53 -n
```


### Wireshark 
- way more informative than tcpdump, but restricted to GUI environments 