---
class: 
topics: 
creation_date: 2025-01-08 09:56
last_updated: 2025-01-08 09:56
---
#### To Do
- [ ]
---
# Admin
- attendance is taken
- lectures (on Thursday) can attend through zoom or in person 
- 10 labs 1 project 
	- found under assignment 


### Contact 
- email: lisa.li2@senecacollege.ca 
- office hours
	- by appointment (for private matters) 
	- technical questions should be made during lab hours 

### Readings
- Network Basics Companion Guide, Cisco Networking Academy 
- CCENT/CCNA ICND1 100-105
- CCNA 200-301 cert guide 
- NOTE:
	- readings are recommended. if you don't like them you are welcome to find other sources that help you understand better 


### Labs
- due dates are based on the submission link on blackboard not the outline sheet
- packet tracer is good, but the emphasis of this course is hands on experience with the actual physical devices 
	- use packet tracer for practice 
- if you do labs using only packet tracer, wont be eligible for full credit for the lab 
	- to get full marks must come in person and work on the physical equipment 
- for labs that require equipment that is limited, will have to do the lab in your group 
	- some labs can be done individually
- lab submissions 
	- screenshots
		- good quality 
		- proper zoom level (only important parts)
		- includes name of device 
	- answer questions in different colour 
	- do not remove any part of the original document 

### Project 
- start thinking about your project early 
- 2 stages 
	- 1st stage: due end of first half of semester (around study week) 
		- clear understanding of network design and configuration 
	- 2nd stage: end of semester 
		- final report and presentation (video or in person)
- can do with lab group or can do by yourself
![[Pasted image 20250108103253.png]]
- When creating VMs in virtual box, can change the default name from 'inet' to something else 
	- group segments of the network based on the 'name'
- will be configuring the routers 
	- ex: dynamic, static, etc.. 
- first step
	- setting up the VMs and connecting them 
	- snapshot of physical settings for the VMs 
	- fill out the table 
		- make sure snapshots match the data in the table 


### Physical Lab Setup 
- 9 racks 
	- each 4 computers in a line are connected to one rack 
- 4 computers grouped together and connected to one rack
	- the back ports connect directly to one rack (purple)
		- the letter next to the purple port determines the rack assiciated with it 
	- where you sit determines what rack you are working on 
	- patch panels already connected to each other 
		- but have to connect the host computers to the correct port on the patch panel and the racks as well 
		- port numbers have to match 
			- ex: H1 to H1 
	- 2 cables
		- one console port (flat one) 
			- use putty 
			- use to login to network devices 
			- to interact with the switch (configurations) 
				- console used to manage the device (not send data) 
		- data connection 
			- for actual transmission of data 
- all computers have 3 NICs
	- one is for the Seneca network 
	- can use either of the other 2
- to test, all PCs should be able to ping each other 




### Practical test (20%)
- 3 weeks after reading week 
- covers all labs 
- done on the physical equipment 
- very important and hard 
	- study well
	- pay close attention to labs 
