To Do 
- [x] #lab download Ubuntu 24.04.1 LTS  [due:: 2025-01-08]  [completion:: 2025-01-08]

#### What is Automation 
- using technology to perform tasks with minimal human involvement 
	- main goal and sub tasks 
	- ex: main goal is drive to destination, sub goals include steering the wheel, pressing the gas, signalling and turning etc.. 
	- ex: main goal turn on porch light at 6pm and turn off at 4am, sub tasks 


### Benefits of Automation 
- increase efficiency 
- error reduction 
	- humans produce the most errors
- improves scalability and reliability 
	- can handle increasingly larger workloads, complexity, and users, etc.. 
- simplified complex operations (SCO)
- reproducibility 


### IT Automation Tools 
- ==Ansible==
	- architecture = agentless
		- control node = computer used to manage cluster of automated things 
		- end node = services at the end you control using the control node 
		- (used to be master-slave) 
		- agentless means no agent is required to interface between control node and end node
	- language = YAML
		- YAML is a superset of JSON
	- beginner friendly 
	- push based
		- just have to push updates from control node 
		- pull based = agent on each node pulls updates 
- Puppet
- Chef
- Salt 

## Intro to Ansible 
- open source automation tool 
	- written in Python 
- Red Hat Automation Platform (paid) 
	- provides additional services 
	- NOT used in this course
- Languages: Python and YAML

#### Role of Ansible in Cybersecurity 
- automatically enforce security policy 
- reduce human error 
- scalability 
	- automates security across thousands of systems 
- flexibility 
	- works with variety of OSs, cloud providers and security tools 
- cost-effective 
	- open source, ease of use 
- rapid deployment 
	- quickly deploys security changes or remediations across all systems in mins 

# Hands on Lab Overview 
- lab submission done through github 
	- might be gitlab if cannot use myseneca email 
- lab submissions done in markdown or HTML 
- will be using VMWare 
- download 24.04.1 LTS Ubuntu prior to lab 




