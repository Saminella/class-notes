---
class: srt205
textbook: "ansible: up and running 3rd edition"
chapters: "5"
tags: 
creation_date: 2025-03-09 14:32
last_updated: 2025-03-09 14:32
---
#### To Do
- [ ]
---
# 5 Variables and Facts

# Defining Variables
---
### Defining Variables in Playbooks 
- simplest way is to put variables in `vars:` section in your playbook 

###### Example: defining variables in vars: section of playbook 
```yml
vars:
  tls_dir: /etc/nginx/ssl/
  key_file: nginx.key
  cert_file: nginx.crt
  conf_file: /etc/nginx/sites-available/default
  server_name: localhost
```

---

### Defining Variables in Separate Files
- can put variables in one or more files and reference them in your playbook 
- requires a section called ==`vars_files`==

###### Example: vars_files section in playbook
```yml
vars_files:
  - nginx.yml  # external file that contains variables
```

###### nginx.yml file from above example
```yml
key_file: nginx.key
cert_file: nginx.crt
conf_file: /etc/nginx/sites-available/default
server_name: localhost 
```
- contains the actual variable definitions 

---


### Defining Variables in Inventory 
- can define variables associated with hosts or groups in the inventory 
- requires separate directories that live alongside your inventory hosts file or your playbooks 

#### Example directory structure 
```bash
inventory/
  production/
    hosts
    group_vars/  # define group variables here
      webservers.yml
      all.yml
    host_vars/   # define host variables here
      hostname.yml
```
- files and directories in `group_vars` are matched against the groups defined in the file hosts 
	- inventory variable files MUST have same name as group name
- files and directories in `host_vars` are matched against individual hosts 
	- inventory variable files MUST have same name as host 

---



# Viewing the Values of Variables
---
- can use the `debug` module to print the value of a variable 
- to interpolate the value of a variable, use double-quoted string with variable name in double braces 
	- ex: `msg: "The file used was {{ conf_file }}"`

###### Example: basic `debug` use
```
- debug: var=myvarname
```

###### Example: variable interpolation 
```yml
- name: Display the variable
  debug:
    msg: "The file used was {{ conf_file }}"
```

###### Example: concatenating variables 
```yml
- name: Concatenate variables
  debug:
    msg: "The URL is https://{{ server_name ~'.'~ domain_name }}/"
```

---



# Registering Variables
---
- ==to set the value of a variable based on the result of a task== 
- recall: each Ansible module returns results in JSON format 
	- use a 'registered variable' to use these results 
	- use `register` clause 
- registered variables will ALWAYS be a dictionary data type 
	- but keys of the dictionary depend on the module output registered 


###### Example: capture output of id 
```yml
---
- name: Show return value of command module
  hosts: fedora
  gather_facts: false
  tasks:
    - name: Capture output of id command
      command: id -un
      register: login

    - debug: var=login  # will print entire dictionary in `login`
    - debug: msg="Logged in as user {{ login.stdout }}" # only print stdout
...
```

###### Print contents of `login`  
```bash

TASK [debug] *******************************************************************
ok: [fedora] ==> {
    "login": {
        "changed": true,				1
        "cmd": [	# cmd key contains invoked commands as a list of strings 
            "id",
            "-un"
        ],
        "delta": "0:00:00.002262",
        "end": "2021-05-30 09:25:41.696308",
        "failed": false,
        "rc": 	# return code 				
        "start": "2021-05-30 09:25:41.694046",
        "stderr": "",		  # error will appear here
        "stderr_lines": [],
        "stdout": "vagrant",  # stdout as single string
        "stdout_lines": [     # stdout as list separated by /n
            "vagrant"
        ]
    }
}
```


---

### How to access Dictionaries stored in Variables
- if a variable contains a dictionary, can access in 2 ways:
	- dot (.)
		- `{{ result.stat }}`
	- subscript(\[ ])
		- `{{ result['stat'] }}`
		- can use variables in brackets 
			- cannot do this with dot notation 

###### Example: different ways to access Dictionary variable
```
result['stat']['mode']
result['stat'].mode
result.stat['mode']
result.stat.mode
```

---




# Facts
---
- facts are gathered every time Ansible runs a playbook 
	- gathered before the first task runs 
	- NOTE:
		- not all facts are gathered every time Ansible runs a playbook!
		- some facts require you to explicitly gather them 
			- ex: `ansible_facts.packages` requires you to explicitly gather facts using the `package_facts` module 
			- `service_facts` also need to be gathered 
- how does Ansible gather facts
	- connects to the hosts and queries it for all kinds of details 
- can access fact info in the `ansible_facts` variable 
	- `ansible_prefix` 
		- access Ansible facts as top-level variables 
- any module can return facts or info 
	- do not have to register variables for modules that return facts 
	- ansible creates the variables for you automatically 


---

### `setup` module 
- fact collection is implemented using the `setup` module 
	- called automatically every time facts are gathered 
- to invoke manually ad hoc:
	- ex: `ansible ubuntu -m setup`
- the returned value is a _dictionary_ whose key is `ansible_facts`

##### filter parameter for `setup` module 
- the `setup` module supports a `filter` parameter because it returns so much data 
- lets you filter by fact name, or by specifying a glob 
- ONLY filters the first level subkey below `ansible_facts` 

###### Example: ad hoc filter 
```
ansible all -m setup -a 'filter=ansible_all_ipv6_addresses'
```

###### Output 
```bash
debian | SUCCESS => {
    "ansible_facts": {
        "ansible_all_ipv6_addresses": [
            "fe80::a00:27ff:fe8d:c04d",
            "fe80::a00:27ff:fe55:2351"
        ]
    },
    "changed": false
}
fedora | SUCCESS => {
    "ansible_facts": {
        "ansible_all_ipv6_addresses": [
            "fe80::505d:173f:a6fc:3f91",
            "fe80::a00:27ff:fe48:995"
        ]
    },
    "changed": false
}
ubuntu | SUCCESS => {
    "ansible_facts": {
        "ansible_all_ipv6_addresses": [
            "fe80::a00:27ff:fef1:d47",
            "fe80::a6:4dff:fe77:e100"
        ]
    },
    "changed": false
}
```



###### Example: the `service_facts` module returns facts by default (requires no register keyword)
```
- name: Show a fact returned by a module
  hosts: debian
  gather_facts: false
  tasks:
    - name: Get services facts
      service_facts:

    - debug: var=ansible_facts['services']['sshd.service'] 
```
- requires NO register keyword
###### Output:
```bash
TASK [debug] *******************************************************************
ok: [debian] ==> {
    "ansible_facts['services']['sshd.service']": {
        "name": "sshd.service",
        "source": "systemd",
        "state": "active",
        "status": "enabled"
    }
} 
```


---



### Additional Info About Facts and Fact Gathering 
- some facts are not gathered automatically when Ansible runs a playbook 
	- examples: 
		- `package_facts`
			- to access: `ansible_facts.packages`
		- `service_facts`
			- to access: `ansible_facts.services`
			- ex: `ansible_facts.services['sshd']['state']` 
		- `mount_facts`
			- to access: `ansible_facts.mounts`
		- `user_facts` 











### Fact Gathering Examples

#### `ansible_facts.packages` 
- from [[SRT205_Lab7]] 
	- create a task to restart the nginx service when it is in `ansible_facts.packages`
```yaml
	- name: Gather package facts
      package_facts:
        manager: auto 
    - name: show all installed packages
      debug: 
        var: ansible_facts.packages
```
- NOTE:
	- have to gather the package_facts info FIRST to be able to work with it later 
	- `ansible_facts.packages` is NOT collected unless explicitly gathered first 
		- have to use the `package_facts` module to collect package-related facts from the system 
		- then `ansible_facts.packages` becomes available for use in following tasks




























# Define a New Variable using `set_fact`
---
- ==can set a fact in a task by using the `set_fact` module ==
- suggested to use `set_fact` immediately after `service_facts` 
	- makes it simpler to refer to a variable 


###### Example: set_facts 
```
- name: Set nginx_state
  when: ansible_facts.services.nginx.state is defined
  set_fact:
    nginx_state: "{{ ansible_facts.services.nginx.state }}"
```


---





# Built-In Variables
---
- variables that are always available in every playbook 


| Parameter                | Description                                                                                  |
| ------------------------ | -------------------------------------------------------------------------------------------- |
| hostvars                 | a dict whose keys are Ansible hostnames and value as dicts that map variable names to values |
| inventory_hostname       | name of the current host as known in the Ansible inventory, might include domain name        |
| inventory_hostname_short | name of current host as known by Ansible WITHOUT domain name                                 |
| group_names              | list of groups the current host is member of                                                 |
| groups                   |                                                                                              |
| ansible_check_mode       |                                                                                              |
| ansible_play_batch       |                                                                                              |
| ansible_play_hosts       |                                                                                              |
| ansible_version          |                                                                                              |
### Detailed Descriptions 
#### hostvars
- in Ansible variables are scoped by host 
	- variable values are relative to a given host 
- to use variables scoped to other hosts, use the `hostvars` variable 
	- `hostvars` is a dictionary that contains all the variables defined on all the hosts
	- keyed by hostname as known to Ansible 
- only works if Ansible has gathered facts about a host 

```
{{ hostvars['db.example.com'].ansible_eth1.ipv4.address }}
```
- accessing the variable scoped to 'db.example.com' host 











