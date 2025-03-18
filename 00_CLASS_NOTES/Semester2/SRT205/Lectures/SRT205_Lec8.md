---
class: 
topics: 
tags: 
creation_date: 2025-03-17 10:38
last_updated: 2025-03-17 10:38
---
#### To Do
- [ ]
---
# SRT205_Lec8
# Jinja2


# Jinja2 Syntax Basics
---
### About
- spaces after curly braces { } are important 
	- meaningful because indicate where things start and stop for Jinja2


- using variables

```
server_name: {{ inventory_hosts }}
```


- conditionals
	- conditional keywords are surrounded by `{% conditional %}`

```

```



### Filters 
- syntax: `{{ variable | filter_name }}`
- [[Ansible Filtering]] 
- common filters
	- default variable
	- upper 
		- convert to uppercase or lowercase 
	- replace
		- replace parts of a string 
	- join 
		- can `join` a list into a single string
		- converts individual list items into a single string 

Default variable 
```
{{ var | default('default_value') }}
```
- in case the `var` value is not passed or is passed something bad, can add default variable which helps ensure playbook does not fail 






# Using the Template Module
---
### What is the template module
- generates files on remote systems using Jinja2 templates
- more reliable that using the `file` module 
	- look into
- templates are stored in the `templates/` directory of a rule or playbook
	- `.j2` indicates a Jinja2 template 

### Important template Parameters
- `src`
	- path to the Jinja2 template
- `dest`
	- destination path on remote system 
- `owner, group, mode`
	- sets file permissions 













