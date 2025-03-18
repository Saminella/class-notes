---
tags:
  - ansible
  - ansible/loops
aliases:
  - Loops
creation_date: 2025-03-11 14:36
last_updated: 2025-03-11 14:36
---
# Ansible Loops
### 1️⃣ Keywords: 
- `loop`
	- newer syntax
	- purpose:
		- will run the task once per item in the list used as input
- `with_<lookup>`
	- older syntax
	- relies on `lookup` plugins
-  `until` 
	- similar to `while` loop 
	- purpose: 
		- will rerun the task until a condition is met 


#### `loop`
- purpose: 
	- will run the task once per item in the list used as input 
	- similar to a `for loop` 
- how it works: 
	- requires a list as input 
	- loop control DOES affect 



#### `until`
- similar to `while` loop 
- purpose: 
	- will rerun the task until a condition is met 
- how it works: 
	- accepts an 'end condition' 
		- expression that returns True or False 
		- end condition is 'implicitly templated' 
			- no need for {{ }}
	- NOT affected by loop control 
	- has own keywords:
		- `retries`
		- `delay` 





# 2️⃣ Using Loops 
---
### All the things you can iterate over using loops: 
- lists
- list of hashes 
	- same as a 'list of dictionaries' 
	- a list where each item is a dictionary 
- dictionaries
	- have to use the dic2items 


---

### Iterating Over a List
- can define the list directly in the task under the `loop` keyword 
- can also define the list in an external variables file 
	- or in the `vars:` section of the play 
- sometimes can pass a list directly to a parameter 
	- only works for some plugins 
	- ex: the `package` modules allow for this 
	- if possible, this is better than looping over the task


###### Example: list defined directly in the task 
```yml
- name: Add several users
  ansible.builtin.user:
    name: "{{ item }}"
    state: present
    groups: "wheel"
  loop:
     - testuser1
     - testuser2
```


###### Example: list defined in a variables file or in `vars:` section of play
```yml
loop: "{{ somelist }}"
```


###### Example: passing a list directly to a parameter for a module 
```yml
- name: Optimal yum
  ansible.builtin.yum:
    name: "{{ list_of_packages }}" # Better
    state: present

- name: Non-optimal yum, slower and may cause issues with interdependencies
  ansible.builtin.yum:
    name: "{{ item }}"
    state: present
  loop: "{{ list_of_packages }}"
```


---


### Iterating over a List of Hashes
- list of hashes means a list of dictionaries 
	- each item in the list is a dictionary
- can reference dictionary subkeys in the loop


###### Example: iterating over list of hashes defined directly in the task 
```yml
- name: Add several users
  ansible.builtin.user:
    name: "{{ item.name }}"
    state: present
    groups: "{{ item.groups }}"
  loop:
    - { name: 'testuser1', groups: 'wheel' }
    - { name: 'testuser2', groups: 'root' }
```

###### Example: list of hashes defined in `vars:` section of play
```yml
- name: Define users
  set_fact:
    users:
      - name: "alice"
        uid: 1001
        shell: "/bin/bash"
      - name: "bob"
        uid: 1002
        shell: "/bin/zsh"

- name: Create users
  user:
    name: "{{ item.name }}"
    uid: "{{ item.uid }}"
    shell: "{{ item.shell }}"
    state: present
  loop: "{{ users }}"
```
- NOTE:
	- `{{ users }}` at the very bottom refers to the 'users' list defined in set_facts



---


### Iterating over a Dictionary 
- use `dic2items` to loop over a dictionary
	- converts a dictionary into a list of dictionaries (list of hashes from above)
- NOTE:
	- `dic2items` ALWAYS uses the keys: `item.key` and `item.value` 


###### Example: dictionary defined directly in the task 
```yml
- name: Using dict2items
  ansible.builtin.debug:
    msg: "{{ item.key }} - {{ item.value }}"
  loop: "{{ tag_data | dict2items }}"
  vars:
    tag_data:
      Environment: dev
      Application: payment
```
- NOTE:
	- `item.key` expands to 'Environment' and 'Application' 
	- `item.value` expands to 'dev' and 'payment' 


###### Example: dictionary defined in `vars:` section 
```yml
users: # defined in vars: section 
  alice: { uid: 1001, shell: "/bin/bash" }
  bob: { uid: 1002, shell: "/bin/zsh" }

- name: Convert dictionary to a list
  debug:
    msg: "{{ item.key }} has UID {{ item.value.uid }} and uses {{ item.value.shell }}"
  loop: "{{ users | dict2items }}"
```
- NOTE:
	- to access the different values (ex: 'uid' and 'shell') use dot notation further 
	- ex: 
		- `item.value.uid`
		- `item.value.shell` 

---







# 3️⃣ Loop Control 
---
### Main Types of Loop Control in Ansible
- limiting iterations
- skipping items
	- maybe use for [[SRT205_Lab7]]? 
- labeling output
	- `label`
- delaying execution 
	- `pause`
- controlling parallelism 





### `label`: Limit Loop Output
- when looping over complex data structures, console output of your task can be enormous 
	- `label` allows you to manage output so you only see the relevant stuff 
- purpose:
	- make output easier to read
	- help with debugging 

###### Example:
```yml
- name: Create servers
  digital_ocean:
    name: "{{ item.name }}"
    state: present
  loop:
    - name: server1
      disks: 3gb
      ram: 15Gb
      network:
        nic01: 100Gb
        nic02: 10Gb
        # ...
  loop_control:
    label: "{{ item.name }}"
```
- NOTE:
	- will only print the `item.name` even though lots of other values are looped over as well


---

### `pause`: Pausing within a Loop 
- purpose:
	- control the time (in seconds) between the execution of each item in a task loop 

###### Example:
```yml
# main.yml
- name: Create servers, pause 3s before creating next
  community.digitalocean.digital_ocean:
    name: "{{ item }}"
    state: present
  loop:
    - server1
    - server2
  loop_control:
    pause: 3
```



---


















# 4️⃣ Lookup Functions
---
### About Lookup Functions
- part of Ansible's 'lookup plugins' 



### `query()`
- a lookup function that retrieves data dynamically 
- when used in a loop:
	- returns a _**list**_ of values, which Ansible then iterates over 
	- unlike `lookup()` which returns a single value (long string) 
- example use cases:
	- to fetch data dynamically from files, directories or external sources 
- NOTE:
	- query() runs on the control node NOT managed nodes 
		- therefore, if using a path, the path must exist on the local machine (control node)
		- if using a file, the file must exist on the local machine (control node) 

###### Example: query all .conf files in the /etc/myapp/ location on the CONTROL node 
```yml
- name: Read all files in a directory
  debug:
    msg: "Found file: {{ item }}"
  loop: "{{ query('fileglob', '/etc/myapp/*.conf') }}"
```
- query() runs on the controller, not managed nodes! 
	- to query files on a remote host, use [[Ansible Modules#Modules###find|find]] instead




















# References
1. 