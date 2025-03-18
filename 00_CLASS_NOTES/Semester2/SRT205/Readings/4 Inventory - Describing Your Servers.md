---
class: srt205
textbook: "ansible: up and running 3rd edition"
chapters: "4"
tags: 
creation_date: 2025-03-09 11:08
last_updated: 2025-03-09 11:08
---
#### To Do
- [ ]
---
# 4 Inventory - Describing Your Servers

### About Ansible Inventory 
- collection of hosts that Ansible knows about 
- technically, the simplest inventory is a comma separated list of hostnames in an ad hoc command 
- Ansible inventory is very flexible 
	- can be a single file, directory, or an executable 
		- some executable are bundled as plugins 
		- plugins allow us to point to a data source (like a cloud provider) to compile the inventory 
	- supports several formats 
- where to store inventory stuff
	- usually best to create a folder to contain all inventory related stuff 


# Different ways to describe hosts 
- inventory/hosts files
	- static inventory 
	- default way 
- dynamic inventory 
	- executable file 
		- therefore Ansible will execute the file instead of read it 
- multiple files 
	- can have multiple inventory files (both static and dynamic) by placing them all in the same directory and configuring Ansible to use that directory as the inventory 
	- can be done via the `inventory` parameter in `ansible.cfg` or by using the `-i` flag on the command line 
		- Ansible will process all of the files and merge the results into a single inventory 


## Inventory / Hosts Files
- the static inventory files 
- simple text files that contain information about hosts 
- NOTE:
	- localhost is added by default 









# Behavioural Inventory Parameters
---

| Name                           | Default         | Description                                                 |
| ------------------------------ | --------------- | ----------------------------------------------------------- |
| `ansible_host`                 | name of host    | hostname or IP address to SSH to                            |
| `ansible_port`                 | 22              | port to SSH to                                              |
| `ansible_user`                 | $USER           | user to SSH to                                              |
| `ansible_password`             | (none)          | password to use for SSH authentication                      |
| `ansible_connection`           | smart           | how Ansible will connect to host                            |
| `ansible_ssh_private_key_file` | (none)          | SSH private key to use for SSH authentication               |
| `ansible_shell_type`           | sh              | shell to use for commands                                   |
| `ansible_python_interpreter`   | /usr/bin/python | python interpreter on host                                  |
| `ansible_*_interpreter`        | (none)          | like `ansible_python_interpreter`, but for other languages  |
#### Detailed descriptions
`ansible_connection`
- Ansible support multiple 'transport' options 
	- mechanisms used to connect to the host 
- 'smart' is the defaut 
	- will check if the local installation of SSH client 












# Groups
---
- usually want to perform configuration actions on a group of hosts
	- purpose of automation 
- `all` group is automatically defined 
	- includes ALL hosts in the inventory 

##### Example inventory file to create a Django App
```
[production]
frankfurt.example.com
helsinki.example.com
hongkong.example.com
johannesburg.example.com
london.example.com
newyork.example.com
seoul.example.com
sydney.example.com
tokyo.example.com
toronto.example.com

[staging]
amsterdam.example.com
chicago.example.com

[lb]
helsinki.example.com

[web]
amsterdam.example.com
seoul.example.com
sydney.example.com
toronto.example.com
vagrant1

[task]
amsterdam.example.com
hongkong.example.com
johannesburg.example.com
newyork.example.com
vagrant2

[rabbitmq]
chicago.example.com
tokyo.example.com
vagrant3

[db]
chicago.example.com
frankfurt.example.com
london.example.com
vagrant3
```


---

### Nested Groups
- Ansible supports defining groups that are made up of other groups 
- the name for the group that contains other groups must also contain the :children keyword

```
[django:children]
web
task
```
- the groups 'web' and 'task' from the example above are grouped under the 'django' group 
	- because they all need to receive Django related updates 













# Hosts and Group Variables 
---
- can set custom variables for specific hosts or a group of hosts 
	- more common to associate variables with groups instead of individual hosts  
- can specify variables in an inventory file (first example) or in separate variable files for individual hosts or groups
	- when specified in an inventory file, there are limited data types (Boolean and strings) 
	- when specified in external variable file for a host or group, can contain more data types (Boolean, strings, lists, dictionaries) 
		- written in YAML


##### Example: specifying group variables in same inventory file
```
[all:vars]
ntp_server=ntp.ubuntu.com

[production:vars]
db_primary_host=frankfurt.example.com
db_primary_port=5432
db_replica_host=london.example.com
db_name=widget_production
db_user=widgetuser
db_password=pFmMxcyD;Fc6)6
rabbitmq_host=johannesburg.example.com
rabbitmq_port=5672

[staging:vars]
db_primary_host=chicago.example.com
db_primary_port=5432
db_name=widget_staging
db_user=widgetuser
db_password=L@4Ryz8cRUXedj
rabbitmq_host=chicago.example.com
rabbitmq_port=5672

[vagrant:vars]
db_primary_host=vagrant3
db_primary_port=5432
db_name=widget_vagrant
db_user=widgetuser
db_password=password
rabbitmq_host=vagrant3
rabbitmq_port=5672
```
- NOTE:
	- in an Inventory file, can variables can only contain Booleans and strings 
		- separate variable files can contain Booleans, strings, lists, and dictionaries 
	- when defining variables for a group in an inventory file, must include the :vars keyword after the group name so those vars apply to the group
		- ex: `[staging:vars]`
		- if just defining a variable for a single host, don't need to include separate section for that host's vars 
			- can be defined inline

---

### Separate Variable Files
- can create a separate variable file for each host and each group 
- must be written in YAML 
- looks for variables files in two directories:
	- host_vars
	- group_vars 
	- NOTE:
		- these directories must be in either the directory that contains your playbooks or the directory adjacent to your inventory file 
			- if there are both, the playbook directory has priority (look up variable precedence)!
- can break a group variable file into multiple smaller ones if desired
	- by creating a directory within the group_vars directory to hold all individual variable files 


##### Example: group_vars/production
```
---
db_primary_host: frankfurt.example.com
db_primary_port: 5432
db_replica_host: london.example.com
db_name: widget_production
db_user: widgetuser
db_password: 'pFmMxcyD;Fc6)6'
rabbitmq_host: johannesburg.example.com
rabbitmq_port: 5672
...
```



##### Example: group_vars/production (with dictionaries)
```
---
db:
  user: widgetuser
  password: 'pFmMxcyD;Fc6)6'
  name: widget_production
  primary:
    host: frankfurt.example.com
    port: 5432
  replica:
    host: london.example.com
    port: 5432
rabbitmq:
  host: johannesburg.example.com
  port: 5672
...
```
- NOTE
	- variables are accessed differently when using Dictionaries
		- can use 'dot notation':
			- `"{{ db.primary.host }}"`
		- or can access like: 
			- `"{{ db['primary']['host'] }}"`
		- how you would otherwise access variables (NOT dictionary) 
			- `"{{ db_primary_host }}"`


---




# Dynamic Inventory
---
- if you have an system external to Ansible that keeps track of hosts, better to use dynamic inventory to avoid duplication problems 
	- ex: 
		- if hosts run on Amazon EC2, use EC2's web interface, its Query API, or command line tool like awscli to retrieve info about hosts 
		- Cobbler or Ubuntu Metal as a Service (MaaS) 
- dynamic inventory file is an executable 
	- Ansible executes it, does not read it 
- use Plug-ins to dynamically connect to cloud systems 














# Examples
---
##### Most basic inventory entry:
```
192.168.0.1
```
- technically this is the most basic inventory entry 
	- all you need is either the IP address, hostname, or FQDN
- but its highly recommended to add extra details like below for easier management 


##### Specify an alias for an inventory entry: 
```
webserver ansible_host=192.168.0.1
```
- `webserver` is the alias for this entry 
	- when you refer to this host using Ansible, can use this alias 
- `ansible_host` 
	- the inventory parameter that specifies which host to connect to 
	- can be either an IP address or FQDN


##### Grouping inventory entries 
```
[web]
webserver1 ansible_host=192.168.0.1

[database]
dbserver1 ansible_host=192.168.20.1
```
- `[web]` and `[database]`
	- the 'group header' s
	- not required but highly useful 
	- purpose:
		- groups multiple hosts together under a logical name 
		- allows you to refer to the group in your playbook
			- instead of listing individual hosts 


















