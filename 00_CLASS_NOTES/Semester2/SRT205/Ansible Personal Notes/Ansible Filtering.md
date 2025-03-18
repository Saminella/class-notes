---
tags:
  - ansible/filtering
  - ansible
aliases: 
creation_date: 2025-03-11 14:57
last_updated: 2025-03-11 14:57
---
# Ansible Filtering
https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_filters.html
- official documentation for Ansible filtering 





## `|` pipe operator
- used for filtering in Ansible 
	- can use to filter data in when statements
	- applies to [[Ansible Loops|Loops]], [[Ansible Conditionals|Conditionals]], etc.. 
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






# Common Filters

### `flatten()`
- a filter that converts nested lists into a single flat list 
- purpose:
	- ensures all elements are at the same level 
		- instead of being grouped inside sublists 


###### Example: BEFORE flattening
```yml
- name: Define a nested list
  set_fact:
    mylist: 
      - [ "apple", "banana" ]
      - [ "orange", "grape" ]

- name: Debug without flattening
  debug:
    var: mylist
```

###### Output:
```yml
[
  [ "apple", "banana" ],
  [ "orange", "grape" ]
]
```


###### Example: AFTER flattening 
```yml
- name: Flatten the nested list
  set_fact:
    mylist_flat: "{{ mylist | flatten }}"

- name: Debug after flattening
  debug:
    var: mylist_flat
```

###### Output:
```yml
[
  "apple",
  "banana",
  "orange",
  "grape"
]
```









# References
1. 