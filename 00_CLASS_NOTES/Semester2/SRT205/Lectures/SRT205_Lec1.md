---
class: 
topics: Ansible basics
tags:
  - ansible
  - ansible/inventory
creation_date: 2025-01-13 10:16
last_updated: 2025-01-13 10:16
---
#### To Do
- [x] #quiz Quiz 1 (Labs 1 and 2 and chapters 1 and 2)  [due:: 2025-01-20]  [completion:: 2025-01-20]
---
# SRT205_Lec1
# Ansible Basics 
- inventory management with Ansible 

### Agentless Architecture 
- no agents needed on managed nodes 
	- don't have to install additional software to manage nodes 
- communicates directly with servers 
- advantages 
	- less overhead on servers
		- updates automatically done (good for security)  
	- reduced security risks 
		- less attack surface 
	- uses SSH (Linux) or WinRM (Windows)

#### SSH (Secure Shell) for Linux
- Ansible uses SSH to connect to Linux systems 
- SSH is a widely accepted protocol that ensures data confidentiality and integrity 
	- creates secure tunnel between server and host so communication between two devices is encrypted
- no need for additional configuration on most Linux servers 
	- SSH usually installed automatically 

#### WinRM (Windows Remote Management) for Windows
- Ansible uses WinRM to connect to and manage Windows systems 
	- therefore, if managing Windows servers, use WinRM
- a protocol that allows for remote management of Windows servers and workstations 
- configuration is required to enable and setup WinRM on Windows machines for Ansible to manage 
	- no enabled by default 
		- configure firewalls
		- script is provided by Ansible (but not recommended)
- supports PowerShell 

## Installing Ansible 
#### on Linux 
- use Package Manager
	- apt (deb/ubuntu) 
- using pip
	- flexible method, useful for specific versions 
	- pip is a packet manager for Python modules 

#### on macOS
- using Homebrew 

#### on Windows
- Windows not supported for control node 
	- BUT can use Windows subsystem for Linux (WSL2)
- enable WSL3 and installing Ubuntu through WSL2
- installing Ansible on Ubuntu environment running within Windows 
	- host virtual ubuntu VM to manage 

#### Note About Using Python Virtual Environments
- good if using Ansible on host PC
- isolates projects and dependencies 
- why use Virtual Environments
	- isolates projects and dependencies 
- commands:
	- $ python3 -m venv .venv
	- $ source .venv/bin/activate
	- $ pip install ansible 


# Inventory Management
- https://docs.ansible.com/ansible/latest/index.html
	- for Ansible documentation 
- For examples
	- https://docs.ansible.com/ansible/latest/collections/ansible/builtin/ini_inventory.html
		- very helpful
![[Pasted image 20250113105017.png]]

### What are Inventory Files
- lists managed nodes for automation
- 2 types:
	- static 
		- mainly working with static in this class
	- dynamic 
		- requires python programming skills 

### Creating Inventory Files 
- can use YAML or INI for static inventory files 
- the INI file format 
	- widely used by Windows for configuration settings in system and application files 
	- designed for simple configuration management 
	- provides a human-readable and easy-to-edit structure 
	- NOTE:
		- look into 

##### INI files 
- broken into 'sections' 
	- sections defined by \[ ]
	- used to group related settings
- key-value pairs:
	- stored as key = value
	- simple mapping between a setting and its value 
	- within a 'section'
- comments
	- lines starting with ; or \# are ignored by parsers 
- example:
	- ![[Pasted image 20250116114046.png]]
		- \[webservers] 
			- the section
### Managing Server Information in Ansible 
- connection and authentication information 
	- hostname, SSH keys, ports, etc.. 
- can break servers into groups to simplify automation 
	- ex: database, web, email, etc.. 
- privilege escalation 
	- ex: sudo, su, become
	- can define in inventory
- custom variables 
	- can define in inventory files 

### Inventory Parameters 
##### Connection Parameters
![[Pasted image 20250116114202.png]]


##### Privilege Escalation Parameters (sudo/su/become) 
![[Pasted image 20250116114417.png]]
- ansible_become
	- to enable privilege escalation, set to ==yes==

#### Windows-Specific Parameters (WinRM)
![[Pasted image 20250116114717.png]]
- ansible_connection parameter 
	- for Linux, this is automatically SSH
	- BUT for Windows, have to manually change to ==winrm== 

#### Other Parameters 
![[Pasted image 20250116114749.png]]
- ansible_python_interpreter
	- use to ensure a specific python version is used all the time 


NOTE
- can include the same node in multiple groups 




