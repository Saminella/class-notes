---
tags:
  - ansible
  - ansible/conditionals
  - jinja2
aliases:
  - Conditionals
creation_date: 2025-03-09 17:17
last_updated: 2025-03-09 17:17
---
# Conditionals
### About Conditionals in Ansible
- Ansible uses Jinja2 tests and filters in conditionals 



# Basic Conditionals with `when`
- simplest condition statement applies to a single task 
- when you run the task or playbook, Ansible evaluates the test for all hosts 
	- on any host where the test passes (returns True), Ansible runs that task 









### Jinja2 Conditionals
https://jinja.palletsprojects.com/en/stable/templates/#builtin-tests
- for 'built in tests' see above link 
- can also make custom tests but not sure how yet
#### `in` operator
- the `in` operator is placed between two values 
- can be used to check if the value on the left is contained in the value on the right 
	- can use to test if element appears in the list 
	- or if key exists in a dictionary 
- ex: `when: "'nginx' in ansible_facts.packages"`






# Conditional Operators
---
## `in` operator
- part of Jinja2 syntax
- the `in` operator is placed between two values 
- can be used to check if the value on the left is contained in the value on the right 
	- can use to test if element appears in the list 
	- or if key exists in a dictionary 
- ex: `when: "'nginx' in ansible_facts.packages"`

## `|` pipe operator
- used for filtering in Ansible 
	- can use to filter data in when statements
- examples:
	- `when: temperature | float > 90`
		- the variable `temperature` is passed through the `float` filter 
			- if `temperature` is a string (ex: "95.5"), `float` converts it to a float (95.5)
			- if `temperature` is already a number (ex: 95.5) `float` does nothing 
			- if `temperature` is `None` or an invalid value, Ansible may fail
		- if temperature | float is greater than 90, the condition evaluates to TRUE and the task runs
	- `when: disk_space | int < 10`
		- ensures `disk_space` is treated as an int before checking if less than 10
	- `when: user_age | default(0) | int > 18`
		- if a variable is undefined, `default()` prevents failure 











# Conditionals Based on `ansible_facts`
---
- recall facts are attributes of individual hosts 
	- facts that are commonly used in conditional statements: https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_conditionals.html#commonly-used-facts 
	- not all facts exist for all hosts 
		- use `debug` task to determine what facts are available on your system 

###### Example: to see what facts are available on your systems:
```yml
- name: Show facts available on the system
  ansible.builtin.debug:
    var: ansible_facts
```


###### Example: simple conditional based on a fact
```yml
tasks:
  - name: Shut down Debian flavored systems
    ansible.builtin.command: /sbin/shutdown -t now
    when: ansible_facts['os_family'] == "Debian"
```


###### Example: _multiple_ conditions grouped by _parentheses_ 
```yml
tasks:
  - name: Shut down CentOS 6 and Debian 7 systems
    ansible.builtin.command: /sbin/shutdown -t now
    when: (ansible_facts['distribution'] == "CentOS" and ansible_facts['distribution_major_version'] == "6") or
          (ansible_facts['distribution'] == "Debian" and ansible_facts['distribution_major_version'] == "7")
```

###### Example: use _logical operators_ to combine conditions
```yml
tasks:
  - name: Shut down CentOS 6 systems
    ansible.builtin.command: /sbin/shutdown -t now
    when:
      - ansible_facts['distribution'] == "CentOS"
      - ansible_facts['distribution_major_version'] == "6"
```







# Using Conditionals in Loops
---
- if a `when` statement is combined with a loop Ansible processes the condition separately for each item 
	- allows you to execute the task on some items in the loop and skip over others 

###### Example: loop that only executes task when the item is greater than 5
```yaml
tasks:
    - name: Run with items greater than 5
      ansible.builtin.command: echo {{ item }}
      loop: [ 0, 2, 4, 6, 8, 10 ]
      when: item > 5
```
- NOTE:
	- do not need curly braces and quotes around `item` when used in a `when` condition 
		- BUT if embedded in a string, yes 










# ✨Inline Conditionals 
---
- https://learning.oreilly.com/videos/automation-with-ansible/9781800206496/9781800206496-video14_3/
	- only resource I could find on this 
	- can also refer to Jinja2 documentation, but nothing on the Ansible documentation 

###### Example
```yml
- name: Simple inline conditional statement
  vars:
    x: 15
    y: 9

  tasks:
    - debug: "The larger number between {{ x }} and {{ y }} is: {{ x if (x > y) else y }}"
```
- the inline conditional statement must be surrounded by double curly braces {{ }}
	- cannot contain double quotes inside, must be single quotes 

###### Example: from [[SRT205_Lab7]] 
```yml
- name: Install Apache on Debian and NGINX on RedHat 
  package:
    name: "{{ 'apache2' if (ansible_facts['os_family'] == 'Debian') else 'nginx' }}" 
```
- will install `apache2` if the OS is Debian, if any other OS will install `nginx` 









# References
1. 