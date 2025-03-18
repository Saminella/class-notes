---
class: 
topics: 
tags: 
creation_date: 2025-03-03 10:01
last_updated: 2025-03-03 10:01
---
#### To Do
- [ ]
---
# SRT205_Lec6
# Windows Management
windows server 2020
- but can use different version for the project? 
- Ansible can manage any Windows node


### Why use Ansible for Windows Automation
- requires additional configuration 
	- have to enable windows on control node
	- might have to open certain ports 
	- will be provided with a script that will do all windows configurations setup
		- NOT recommended for production use 
			- can be insecure 
		- provided by Ansible 
- centralized management of Windows systems from _**Linux**_ control node 
- Ansible can interact with PowerShell by default 
	- unlike Python, which requires further configurations to work properly 
	- if there aren't modules available, use PowerShell instead of python when possible 

### Common use cases
- manage Windows services
- installing and managing software
- configure files, directories, and registry settings
- automating repetitive tasks using playbooks


# Requirements
---
### Control Node requirements
- ansible installed on Linux control node 
- Python 3.x installed 
- pywinrm Python library for WinRM communication 
	- facilitates communication between control node and windows managed node
	- `pip install pywinrm` 

### Managed Node requirements
- Windows OS installed and accessible 
- PS remoting enabled using: `ConfigureRemotingForAnsible.ps1`
	- script is provided by Ansible community 
- network connectivity between control node and Windows host 

##### ConfigureRemoteForAnsible
- a PS script to simplify setup 
- configures:
	- WinRM listeners (HTTP/HTTPS)
	- firewall rules
	- TrustedHosts settings for cross-domain or non-domain environments 
- steps to run script:
	- download script from Ansible GitHub repo
		- can download using Curl 
		- from class repo
	- run as admin 


### Why use PS
- win_shell 
- natively supported
	- unlike python 

set ansible_connection: winrm
- in inventory file 

### Inventory file
in the individual host files 
- define the specifics for each managed node in separate inventory files
- in the hosts file, create groups and refer to the managed nodes from there
	- name of managed nodes in hosts should be same as the name of the inventory file 



### Commom Windows Modules
- win_ping
- win_service
	- read documentation 
		- because Windows services are different than Linux 
	- commonly used to stop the automatic windows update
		- so you can schedule updates manually 
		- when updates occur by default, they can mess up your configurations
- win_chocolatey 
	- equivalent to `apt` with Linux 
	- when running for the first time may take extra time
		- has to install win_chocolatey if not already installed 
- win_file
- win_user
- win_registry
- win_hotfix
- win_command 
	- execute commands that are not PowerShell-specific 
- win_shell
	- to execute 



can use a script to install 

for AWS cloud 
- make sure to SET LIMIT
- and use free version 
- can get very expensive if you go over 






