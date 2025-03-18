---
tags:
  - ansible/inventory
  - ansible
aliases: 
creation_date: 2025-03-04 10:07
last_updated: 2025-03-04 10:07
---
# Inventory
# 1️⃣ Inventory Concepts
---
- purpose:
	- Ansible automates tasks on managed nodes using a list, or group of lists, known as inventory 
- _groups_ allows you to run automated tasks on multiple hosts at the same time
	- [[#Groups]]
	- once inventory is defined, use patterns to select the hosts or groups you want Ansible to run against 
	- a host can exist in more than 1 group
- types of inventory files:
	- [[#Static vs Dynamic]]
	- static 
	- dynamic 
- where to store inventory files 
	- [[#Inventory Directory Structure]] 
	- though they can exist anywhere, convention to create an `inventory` directory in the project directory that contains all inventory related things 
		- can keep inventory variables in centralised place 
- can add a range of hosts to an inventory file 
- supported formats
	- INI
	- YAML
- can create inventory variables
	- [[#3️⃣ Inventory Variables]] 


---


### Groups
- considerations when creating groups 
	- what 
		- an application, stack, or microservice
		- ex: database server, web server, etc..
	- where
		- a datacenter or region, to talk to local DNS, storage, etc..
		- ex: East, West 
	- when
		- the development stage 
			- to avoid testing on production resources
		- ex: prod, test, etc.. 
- properties about groups
	- a host can exist in more than 1 group 
	- can make groups of groups 
		- parent/child group relationships 
	- there are 2 default groups (`all` and `ungrouped`) 


##### Grouping Groups (parent/child group relationships) 
- can create parent/child relationships among groups 
- 'Parent groups' 
	- also known as: nested groups, group of groups 
- ex:
	- if all production hosts are already in groups like `atlanta_prod` and `denver_prod`, can create a parent group called `production` that includes these smaller groups 
- NOTE:
	- a host that is a member of a child group is automatically a member of the parent group
	- groups can have multiple parents and children
		- but not circular relationships 
	- hosts can be in multiple groups BUT there will only be _one_ instance of a host at runtime
		- Ansible merges the data from multiple groups 

YAML nested group example: 
```yml
ungrouped:
  hosts:
    mail.example.com:
webservers:
  hosts:
    foo.example.com:
dbservers:
  hosts:
    one.example.com:
east:
  hosts:
    foo.example.com:
    one.example.com:
west:
  hosts:
    bar.example.com:
    three.example.com:
prod:
  children: # children: entry for YAML
    east:   # 'east' group is now part of 'prod' parent group
test:
  children:
    west:   # 'west' group now part of 'test' parent group
```


INI nested group example
```ini
[CSR-Routers]  # Group name
CSR-1 ansible_host=192.168.1.210  # CSR-1 is the name
CSR-2 ansible_host=192.168.1.211

[Switches]  # Group name
SW1 ansible_host=192.168.1.150

[LAB:children] # A group called LAB that contained other groups
CSR-Routers
Switches
```

##### Default Groups 
- Ansible creates the `all` and `ungrouped` groups by default 
	- `all`
		- contains every host
	- `ungrouped` 
		- contains all hosts that don't belong to another group except `all` 
- these groups are implicit 
	- don't appear in group listings in inventory files 
- therefore, every host will always belong to at least 2 groups 



---



#### Static vs Dynamic 
- https://blog.devops.dev/ansible-inventory-dynamic-vs-static-7f40e4994b4d
- static
	- manually created 
	- pros
		- simple 
		- easy to create and maintain for small scale setups 
	- cons
		- not suitable for environments where hosts are constantly changing, getting added, removed etc.. 
		- not good for large scale
- dynamic
	- generated through a query by pulling managed node data from a specific source 
		- 2 ways to get data from managed nodes:
			- inventory scripts 
			- plugins
	- very useful when working with the cloud
		- can dynamically add hosts and change details based on cloud API



---







# 2️⃣ Basic Inventory Management
---
### Inventory Directory Structure
```bash
inventory/
  openstack.yml          # configure inventory plugin to get hosts from OpenStack cloud
  dynamic-inventory.py   # add additional hosts with dynamic inventory script
  on-prem                # add static hosts and groups
  parent-groups          # add static hosts and groups
```
NOTE:
- better to split inventory into multiple files instead of a single giant inventory file 
	- easier to manage 
	- more scalable   
	- more modular use across multiple projects 
	- can combine different inventory sources _types_ in an inventory directory 
		- ex: combine static and dynamic hosts and manage as once inventory 
- by default, Ansible load inventory sources in ASCII order according to file name






to check groups in the inventory:
```
ansible-inventory --graph
```










# 3️⃣ Inventory Variables
--- 

### Defining Variables in INI Format
- with INI format, variables are interpreted differently depending on where they are declared 
	- declared inline with the host 
		- interpreted as Python literal structures 
		- since all in one line, have to put quotes around values that contain white space 
		- ex: strings, numbers, tuples, lists, dicts, booleans, None
	- declared in the `vars:` section 
		- interpreted as a string 
			- ex: `var=FALSE` would produce a string equal to 'FALSE' 
		- accepts ONLY a single entry per line 
- if a variable (in INI) format must be a certain type: 
	- always specify the type with a filter in your task 
- is YAML better for variables?
- better to create separate host and group variable files 
	- MUST use YAML syntax though
	- `group_vars`
	- `host_vars` 



---


### Group Variables
INI example:
```ini
[atlanta]
host1
host2

[atlanta:vars]
ntp_server=ntp.atlanta.example.com
proxy=proxy.atlanta.example.com
```

YAML example:
```yaml
atlanta:
  hosts:
    host1:
    host2:
  vars:
    ntp_server: ntp.atlanta.example.com
    proxy: proxy.atlanta.example.com
```


---

### Inherited Variable Values 
### (group variables for groups of groups)
- can apply variables to parent groups as well as child groups 
- precedence:
	- child group variables will have higher precedence than parent groups variables

```ini
[atlanta]
host1
host2

[raleigh]
host2
host3

[southeast:children] # create parent group first
atlanta
raleigh

[southeast:vars]    # apply vars after 
some_server=foo.southeast.example.com
halon_system_timeout=30
self_destruct_countdown=60
escape_pods=2

[usa:children]
southeast
northeast
southwest
northwest
```


```yaml
usa:
  children:
    southeast:
      children:
        atlanta:
          hosts:
            host1:
            host2:
        raleigh:
          hosts:
            host2:
            host3:
      vars: # inline with 'southeast' group
        some_server: foo.southeast.example.com
        halon_system_timeout: 30
        self_destruct_countdown: 60
        escape_pods: 2
    northeast:
    northwest:
    southwest:
```
- since `vars:` is inline with the `southeast` group, these variables apply at the southeast group level 
	- would override variables applied to the `usa` parent group
	- would be overridden by variables applied to the `atlanta` or `raleigh` groups 


---


### Directory Structure for Separate Inventory Variable Files
- store individual host variable files in: `host_vars`
- store group variable files in: `group_vars` 

```bash
inventory/
  01-openstack.yml          # configure inventory plugin to get hosts from Openstack cloud
  02-dynamic-inventory.py   # add additional hosts with dynamic inventory script
  03-static-inventory       # add static hosts
  group_vars/
    all.yml                 # assign variables to all hosts
```






# 🚨 EXAMPLES
---
### INI vs YAML 
###### INI
```ini
mail.example.com

[webservers]
foo.example.com
bar.example.com

[dbservers]
one.example.com
two.example.com
three.example.com
# To define an 'alias' for a host
jumper ansible_port=5555 ansible_host=192.0.2.50 
```
- `main.example.com` belongs to the `ungrouped` group 
	- but its not shown in the INI format 
###### YAML
```yml
ungrouped:
  hosts:
    mail.example.com:
webservers:
  hosts:
    foo.example.com:
    bar.example.com:
dbservers:
  hosts:
    one.example.com:
    two.example.com:
    three.example.com:
    jumper:   # To define an alias for a host
      ansible_port: 5555
      ansible_host: 191.0.2.50
```









# Examples (static)
---
###### Example from: https://www.youtube.com/watch?v=0MT9WvX_j4Y
```ini
# Ansible hosts file for Home Lab

[CSR-Routers]  # Group name
CSR-1 ansible_host=192.168.1.210  # CSR-1 is the name
CSR-2 ansible_host=192.168.1.211
CSR-3 ansible_host=192.168.1.212
CSR-1.home.local

[Switches]  # Group name
SW1 ansible_host=192.168.1.150

[LAB:children] # A group called LAB that contained other groups
CSR-Routers
Switches
```
NOTE:
- although you don't have to specify a name when creating an inventory entry, it is helpful because it will show up when you execute plays 
	- more readable than looking at IP addresses when a play runs 
- when you target the `LAB` group, both the CSR-Routers and Switches groups will be targeted 
	- because they are part of the LAB group 










# References
1. 