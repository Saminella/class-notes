---
class: 
topics: 
tags: 
creation_date: 2025-02-03 10:46
last_updated: 2025-02-03 10:46
---
#### To Do
- [ ]
---
# SRT205_Lec4
# Variables and Facts 
![[05_Variables_and_Facts.pdf]]


# Variables
---
### Purpose
- avoid hard coding values
- make mode modular and reusable code 
	- ex: can represent file names as variables to make the code reusable 
- easier to update 

### Basic Syntax for Variables
```yml
vars:
  key_file: nginx.key
  cert_file: nginx.cert
```
- variables can be defined in:
	- playbooks 
		- using vars
	- external files 
		- using vars_files
	- inventory files
		- group_vars and host_vars 


### Defining Variables in Playbooks 
##### About inline variables 
- variables can be defined directly within a playbook under the `vars` section

##### Example:
```yaml
vars:
  key_file: nfinx.key
  cert_file: nfinx.crt
task:
  name: Display key file 
  debug: msg="Key file is {{ key_file }}
```
- the ==vars:== keyword 
	- specifies all following indented lines are variables
	- this is how to introduce variables in a playbook 
- NOTE
	- should always be a space surrounding the variable name within the double curly brackets {{ }}

### Defining Variables in Separate Files 
- variables can be stored in separate files using `var_files` and loaded into playbooks 
- advantages:
	- separate variables from tasks 
	- useful for sensitive or reusable data
		- esp for passwords 
- uses the ==vars_files== keyword

##### Example: how to call a variable file
```yaml
vars_files:
  - nginx.yml  # the external file storing the variables
```

Contents of the nginx.yml file 
```yaml
key_file: nginx.key
cert_file: nginx.crt
```


### Defining Variables with Inventory Files 
- can use group_vars and host_vars to store variables specific to a group of hosts or individual hosts in inventory

#### Group and Host variables
- group vars 
	- variables specific to a group 
	- group variable file name should be the same as the group name 
- host vars
	- when only want to specify variables to a specific host 
	- name the host variable file exactly the same as the name for the managed node 

#### Example directory layout 
```
inventory/  # directory just for inventory related stuff
	hosts  # inventory file 
	group_vars/
		webservers.yml
	host_vars/
		server1.yml
```

#### File examples
###### group_vars/webservers.yml
```yml 
server_name: web.example.com
```

###### host_vars/server1.yml
```yml
server_name: server1.example.com
```








What if you define the same variable in different places?
- which value will the variable posses?
	- depends on the method used to define variables (variable precedence)
	- higher precedence will over ride lower precedence variables 








## View and Debugging Variables
---
### The debug module
- ==displays the value of a variable== 

##### Example
```yaml
- name: show variable value
  debug: 
    var: server_name
```
##### Example output
```bash
TASK [Show variable value] ******************************************* 
ok: [server1] => { "server_name": "server1.example.com“ }
```

NOTE
- output will indicate whats going on
- can include a message with the debug module 


## Registering Variables
- used to capture the output of a task into a variable 
- what is 'register' 
	- used to log all outputs of execution (logs)
	- use register to capture output of task and store in a variable 
	- can use to capture data dynamically and use for other things 

##### Example
```yml
- name: Capture output of `whoami` 
  command: whoami 
  register: login_user 
- name: Display the registered output 
  debug: 
	msg: "Logged in as {{ login_user.stdout }}"
```

##### Example output
```bash
TASK [Display the registered output] ********************************* 
ok: [server] => { 
"msg": "Logged in as admin" 
}
```








## Variable Precedence 
![[Pasted image 20250217170841.png]]
- big chart (don't have to memorize)
- make sure the value for the variable you would like to use is the highest precedence to make sure the value is as expected so things work as you intend
- consult the chart! 


![[Architecture-Tower_Variable_Precedence_Hierarchy.png]]
### Examples
- role defaults (2)
	- lowest 
	- like 'catch all'
- inventory files (3)
	- can define vars in inventory files
	- can be called in playbooks 
	- but playbook variable over ride inventory files because higher precedence 
- inventory group_vars/all
	- when you have more complicated inventory structure, easier to create groups within inventory for easier management 
	- can define variables for these 'groups' of inventory files 
- playbook 
	- with more complicated structures, can create groups within a playbook folder 
	- look into 
- inventory group_vars/*
	- individual inventory group files 
	- for project, better to have one group_vars file instead of multiple 
	- a single inventory group_vars file take precedence over ALL inventory group_vars files 
- when defined in an inventory file itself 
- host level has higher precedence over group level 
	- for both inventory and playbooks
	- should name host variable files exactly the same as the name as the host it is associated with 
	- playbook host_vars
	- inventory host_vars 
- task vars
	- when vars are defined in tasks, their scope is only local to the task 
	- but if the same variable was defined outside the task, the value within the task variable will have precedence

set_facts module 
https://docs.ansible.com/ansible/latest/collections/ansible/builtin/set_fact_module.html
- gets info which can then be used
- has one of the highest precedence 

the -e option when executing a playbook has highest precedence
- NOTE
	- purely for debugging purposes! don't use in production
	- for all other things, variables should not be defined adhoc

```bash
ansible-playook site.yml -e "user=extra_vars_user"
```




--- 




# Facts 
---
## What are Facts
- system info collected automatically by Ansible 
- ex: 
	- OS, IP address, CPU, memory, etc..
- facts are stored in the `ansible_facts` variable 


## Accessing Facts
##### Example:
```yml
- name: Display system facts
  debug:
    msg: "OS: {{ ansible_facts['distribution'] }} {{ ansible_facts['distribution_version'] }}"
```
##### Example output:
```bash
OS: Ubuntu 20.04
```



## Gathering Facts
- `setup` module
	- Ansible gathers facts automatically before tasks run
	- you can manually run the setup module to display all facts
- filtering facts
	- filter facts to limit the output 
	- using filter option

2 methods
- setup module 
	- can also be used in an ad hoc command 
- gather facts 

```bash 
ansible -i hosts all -m setup 
```
- the 'setup' module will retrieve all system info



