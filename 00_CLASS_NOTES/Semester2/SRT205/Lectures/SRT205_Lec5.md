---
class: 
topics: 
tags: 
creation_date: 2025-02-10 10:06
last_updated: 2025-02-10 10:06
---
 #### To Do
- [ ] 1, 2, 3, 4, 10 (for midterm in 2 weeks) create a new managed node with specific name and perform some tasks on the managed node 
	- [ ] bring control node on hard drive and distro (ISO 24.04 LTS desktop) so you can create managed node on computer
	- [ ] 1 hour
	- [ ] submission through github 
---
# SRT205_Lec5
![[06_Ansible_Playbooks_2.pdf]]

# Conditionals in Ansible 
## What are conditionals?
- similar to loops in programming 
	- but syntax is different 

``` yaml
- name:
  ansible.buildin.package:
    name: apache2
    state: present
  when: ansible_os_family == "Debian"
```
- use 'facts' like ansible_os_family for system specific conditions 
- use 'when' to evaluate Boolean expressions 
- use == for condition evaluation, != for negation evaluation (or 'not' keyword) 
	- must have space surrounding == 

### Combining multiple conditions 
```yaml
tasks:
  - name: Shut down CentOS 6 and Debian 7 systems
    ansible.builtin.command: /sbin/shutdown -t now
    when: (ansible_facts['distribution'] == "CentOS" and ansible_facts['distribution_major_version'] == "6") or
          (ansible_facts['distribution'] == "Debian" and ansible_facts['distribution_major_version'] == "7")
```
- and use AND and OR or LISTS

## Why use Loops 
- https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_loops.html
- loops allow you to repeat tasks for multiple items without duplicating code 
- inside a loop is a list 
- use the keyword 'item' 
- NOTE:
	- textbook uses 'items' keywork, but newer to use 'loop'
	- place at the end of task 
		- after when 

```yaml
- name: Add several users
  ansible.builtin.user:
    name: "{{ item }}"
    state: present
    groups: "wheel"
  loop:
     - testuser1
     - testuser2
```


### More complicated loops
```yaml
- name: Add several users
  ansible.builtin.user:
    name: "{{ item.name }}"
    state: present
    groups: "{{ item.groups }}"
  loop:
    - { name: 'testuser1', groups: 'wheel' }
    - { name: 'testuser2', groups: 'root' }
```


### Iterating over a dictionary 
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



### Loop control 
```yml
- name: Create files with labels
  ansible.builtin.copy:
    dest: /tmp/{{ custom_variable }}.txt
	content: "File created for {{ custom_variable }}"
  loop:
	- alpha
	- beta
	- gamma
  loop_control:
    loop_var: custom_variable
	index_var: idx
	label: "Processing {{ custom_variable }}-{{ idx }}"
```

- allows more control over iteration over loop variables 
	- ex: can customize label, what is printed 
ABOUT
- custom_variable 
	- will be expanded to 'alpha', 'beta', 'gamma' 
- idx
	- expands to the index of the variable (0, 1, 2)


## Handlers
- https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_handlers.html  
- will only be executed when all tasks are executed 
	- at the end of the playbook once regular tasks have finished executing 
- triggered only if changes made to managed node 
- can have multiple tasks trigger the same task 
	- but task will still only be executed once 
- the order that handlers are executed is based on their order written
	- not based on which task triggers what handler first 






