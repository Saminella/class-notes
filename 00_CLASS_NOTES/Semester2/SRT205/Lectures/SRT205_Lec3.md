---
class: 
topics: 
tags:
  - ansible
  - ansible/playbook
  - ansible/module
  - yaml
  - school/lecture
creation_date: 2025-01-27 10:12
last_updated: 2025-01-27 10:12
---
#### To Do
- [x] #quiz SRT205 quiz next Monday before lecture  [completion:: 2025-03-18]
- [x] #lab SRT205 lab4 due next Monday  [completion:: 2025-03-18]
---
# SRT205_Lec3
![[04_Ansible_Playbooks.pdf]]


# Playbook
- tasks to be executed and what machines on which to execute 

## Playbook components
#### Plays
- define 
- best practice to only have 1 play in a playbook 
	- for more modular use
- but can have multiple in a playbook

#### Tasks 
- individual actions to be performed 
- one play can contain multiple tasks 

# YAML Basics
- key value pairs (dictionaries)
- key rules
	- uses double spaces for indentation (not tabs)
		- esp when using nano or vim 
	- should always start with ---
		- because one playbook can contain multiple plays 
		- this separates content of plays 
		- however, does still work without the --- a
	- comments start with # 
- can include lists 
- online YAML checker: https://www.yamllint.com/ 

### Boolean values
- many different ways to represent boolean values which are all valid 
- but choose one that makes sense 

### Strings
- use double quotes to negate special characters in a string 
- use single quotes for the special characters to be recognized 
- can also use no quotes 
- multiple lines
	- begin with pipe, | then all following lines are interpreted as single lines
- 
```yaml
multilineString: |
line of text
another line of text
```
- begin with > then following lines are interpreted as a giant paragraph 

# Playbook Structure 
### name
- describes purpose of play 

### hosts
- can be a specific machine 
	- hosts: webservers:
- can be multiple hosts

### become
- elevates privileges 

### tasks
- detailed actions going to do with this play 
- what are tasks
	- basic building blocks of a playbook
	- single action by using modules 
- key components of a task
	- name => description of the task 
	- module 
	- arguments 

```yaml
---
- name: Update web servers
  hosts: webservers
  remote_user: root

  tasks: # task
  - name: Ensure apache is at the latest version
    ansible.builtin.yum: # module that is used
      name: httpd # parameter for the yum module 
      state: latest # parameter for the yum module
```


# Modules 
- what are modules
	- scripts that Ansible uses to perform tasks 

# Executing a Playbook 
- ansible-playbook -i \<inventory file> \<playbook name>.yml
	- have to specify -i \<inventory file> if using different than default


# Output after running playbook
- ok
	- task executed successfully without changes
- changed
	- task made changes to the target host
	- usually dependent on 'state' value 
- ignored
	- each task will produce output when a playbook is run 
		- will indicate if successful or failed
		- if failed, by default no other tasks/plays will run 
	- set 'ignore_errors: yes' in the desired task to continue with following tasks even if this task encounters a fatal error 
		- will indicate after playbook has run the number of tasks that have been ignored
- unreachable error
	- cannot connect to target 
	- indicates something wrong with inventory file or inventory construction 
- failed
- skipped
- rescued 


# Example Playbooks

```yaml
---
- name: Clone a few repos for srt205 class
  hosts: local # can also be a group
  tasks:
    - name: Create a folder 
    ansible.builtin.file:
	  path: /etc/foo.conf 
	  owner: foo
	  group: foo
      mode: '0644' # permissions required. if not specified, will be default umask value for node

# to create the file based off relative location, begin filepath with . 
# ex: ./etc/foo.conf



---
- name: Cloning a few repos for SRT205 class
	vars: # for creation of variables that can be used
	  skip_sean: false # 
	tasks:
	- name: Create a folder
	ansible.builtin.file:
	  state: directory
	  mode: "0755"
	- name: Clone profs repo
		ansible.builtin.git
			repo: "https://github.com/yuhui-feng_seneca/srt205-labs"
			dest: "./repos_example"
	when: skip_sean # when is a keyword, when set to false will skip the task 
	- name: clone to Mr.Singh's repo
		ansible builtin.git: 
```








