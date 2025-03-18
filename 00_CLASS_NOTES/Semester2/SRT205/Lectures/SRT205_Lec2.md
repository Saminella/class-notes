---
class: 
topics: 
tags:
  - ansible
  - ansible/module/ad-hoc
  - ansible/module
creation_date: 2025-01-20 10:54
last_updated: 2025-01-20 10:54
---
#### To Do
- [ ]
---
# SRT205_Lec2

## Modules
- also known as 'task plugins'
- perform specific tasks 
	- ex: installing software, file management, configuration management,
- why use modules?
	- developed to ensure works properly (idempotency)
		- but not all modules are idempotent (ex: apt will not install if the packages are already installed)
	- so you don't have to write scripts that have already been written 
	- simplifies complex tasks 
		- can simply code a module instead of creating lots of lines of code
	- enhance reliability and efficiency of automation tasks
- look for a modules before you create a script 

### Ansible Collections 
- modules are part of Ansible collections 
- a collection is a bundle of content 
- a collection can include many different things like:
	- modules, roles, plugins, playbooks 
- why group modules into collections 
	- simplifies sharing 
	- ...
- built in collections
	- ansible.builtin
		- contains the core modules 
	- community.general
		- widely-used collection with community-contributed modules 
		- over time these well used modules have been grouped together 
		- https://docs.ansible.com/ansible/latest/collections/community/general/index.html#plugins-in-community-general 
			- documentation for this collection 
			- outlines all the modules inside this collection
	- community.mongodb
	- community.mysql
	- amazon.aws
		- modules for interacting with AWS services like EC2, S3, RDS 

#### Where to find Collections
- https://docs.ansible.com/ansible/latest/collections/index.html  


### Example: github repo module
```yaml
- name: Create a Github repository
  community.general.github_repo:
    access_token: mytoken
    organization: MyOrganization
    name: myrepo
    description: "Just for fun"
    private: true
    state: present # will check if the repository is already created, will not do anything if present (if set to create, will create regardless if it already exists)
    force_defaults: false
  register: result

- name: Delete the repository
  community.general.github_repo:
    username: octocat
    password: password
    organization: MyOrganization
    name: myrepo
    state: absent
  register: result
```


### Example: ping built-in module
https://docs.ansible.com/ansible/latest/collections/ansible/builtin/ping_module.html
```yaml
# Test we can logon to 'webservers' and execute python with json lib.
# ansible webservers -m ansible.builtin.ping

- name: Example from an Ansible Playbook
  ansible.builtin.ping:

- name: Induce an exception to see what happens
  ansible.builtin.ping:
    data: crash
```


### Ansible Built-in modules
- built-in modules are pre-packed modules available in the ansible.builtin collection 
- benefits
	- no additional installation required
	- officially maintained with Ansible releases
	- well designed 

#### Key Built-in Modules
###### Ping
- verifies connectivity between control node and managed hosts 

###### Copy 
- copies files from control node to managed node 

###### service
- manages services (ex: start, stop, restart)
- ex:
	- ansible all -m service -a "name=nginx state=started"
		- // will ensure nginx server is set to the 'started' state
		- if already in started state, will remain as such. if not, will change to started

###### apt
- manages packages 
- https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_module.html
- ex:
	- ansible all -m apt -a "name=nginx state=present"
		- if the package is already installed, will not install 
- state=absent
	- will delete the package if present 
- state=present
	- will install package if does not exists 
- Important parameters
	- allow_downgrade
		- lookup 
```yaml
- name: Install the version '1.18.0' of package "nginx" and allow potential downgrades
  ansible.builtin.apt:
    name: nginx=1.18.0
    state: present
    allow_downgrade: yes
```

###### telegram
- to send a message to devices?

###### mail
- will send an email 
- can use to send email to all necessary people after you pushed an update

# Ad-Hoc Commands
### What are Ad-Hoc Commands
- one time ansible commands executed directly from command line 
	- when you only want to execute a command once and don't want to automate 
- NO playbook creation required 
- NOTE:
	- ==when no module is specified, Ansible defaults to the `command` module== 
		- [[Ansible 101]] 

### Ad-Hoc Command Examples
#### Structure of Ad-Hoc Commands
```shell
ansible <target> -m <module name> -a "<module options>"

## <target> 
# -a for all managed nodes
# can specify a particular server by name (as found in inventory file)

## -a
#accepted values either key=value pair or JSON string between {}
```

##### check uptime 
```bash
ansible web_servers -m command -a "uptime"
```
- web_servers => target
- -m => means module 
- -a => means argument 
#### copy a file
```bash
ansible all -m copy -a "src=/etc/hosts dest=/tmp/hosts"
```
- -a => arguments
	- can match parameters to values 
		- ex: src parameter
- all => target
#### restart a service 


