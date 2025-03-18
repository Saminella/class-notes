---
class: srt205
textbook: "ansible: up and running 3rd edition"
chapters: "2"
tags: 
creation_date: 2025-03-08 12:18
last_updated: 2025-03-08 12:18
---
#### To Do
- [ ]
---
# 2 Installation and Setup
### About Ansible
- Ansible is written in Python 
- usually don't need to install anything on target systems 
	- so long as they run Linux/macOS/BSD and have Python installed
	- for Windows, must have PowerShell



### Telling Ansible About Your Servers
- Ansible can only manage servers it explicitly knows about 
- specify information about servers in the 'inventory' 
	- usually stored in a _inventory_ directory
- can group servers 



### ansible.cfg File 
- usually placed in current directory with playbooks 
- can do:
	- specify location of inventory file 
	- set parameters that affect the way Ansible runs 
		- ex: how output is presented 






