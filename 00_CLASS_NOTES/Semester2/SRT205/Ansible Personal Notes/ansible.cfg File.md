---
tags:
  - ansible
  - configuring
aliases: 
creation_date: 2025-03-15 10:45
last_updated: 2025-03-15 10:45
---
# ansible.cfg File
### About
- the configuration file for Ansible itself 
- determines how Ansible operates 
	- the default behaviour for Ansible
	- ex: where to look for default inventory file, where private key is located, etc.. 
- can create your own ansible.cfg files
	- the one in the local directory take precedence over the default one in /etc/ansible/ansible.cfg
		- the one located in the local projects directory 
- NOTE:
	- dependent on where you execute `ansible` or `ansible-playbook`! 
		- if an `ansible.cfg` file exists in the local directory where you execute `ansible` or `ansible-playbook`, this `ansible.cfg` file will take highest precedence 
		- if no `ansible.cfg` file exists in the local directory, Ansible looks in other places in the following order: 
			- environment variable path (if set) 
				- `ANSIBLE_CONFIG`
			- user home directory 
				- `~/.ansible.cfg`
			- global system-wide config 
				- `/etc/ansible/ansible.cfg`
				- default location for `ansible.cfg`





# Examples
---

```ini
[defaults]
inventory = ./hosts
forks = 3
timeout = 20
host_key_checking = false
retry_files_enabled = false 
```
ABOUT
- `inventory = ./hosts`
	- specified the default inventory file 
	- if a relative path, its relative to the current working directory where the playbook or ad hoc command was executed
		- `./hosts` means 'hosts file in current working directory' 
			- in other words, will look where you run `ansible` or `ansible-playbook` 














# References
1. 