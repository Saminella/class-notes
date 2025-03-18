---
class: srt205
topics: 
tags: 
creation_date: 2025-03-10 10:08
last_updated: 2025-03-10 10:08
---
#### To Do
- [ ]
---
# SRT205_Lec7

# Playbook Reusability 
(Chapter 9)
- Ansible roles
	- pretasks and post tasks (not tested)

# Roles
- way to group things
	- ex: tasks, variables, handlers etc..
	- based on functionality or role 
- purpose:
	- modularity 
	- easier to manage (maintainability) 
		- multi member team and future you 
	- portable 
		- can use same roles for diff projects 
	- scalability 
- can download roles that have already been made by others 
	- why metadata is important for roles 
		- ex: dependencies 
	- use `ansible-galaxy` 
		- https://docs.ansible.com/ansible/latest/cli/ansible-galaxy.html 
		- `ansible-galaxy init --init-path playbook/roles/ webserver`
			- has to be within playbook directory 
			- will create a 'webserver' role 
- instead of having a playbook with a lot of tasks
	- can simply state what roles are applied 
- how to group roles?
	- based on functionality 
	- based on managed nodes 
	- try to design roles that are reusable for other projects 


### Role Structure 
- directory structure 

```
roles/
    common/               # this hierarchy represents a "role"
        tasks/            #
            main.yml      #  <-- tasks file can include smaller files if warranted
        handlers/         #
            main.yml      #  <-- handlers file
        templates/        #  <-- files for use with the template resource
            ntp.conf.j2   #  <------- templates end in .j2
        files/            #
            bar.txt       #  <-- files for use with the copy resource
            foo.sh        #  <-- script files for use with the script resource
        vars/             #
            main.yml      #  <-- variables associated with this role
        defaults/         #
            main.yml      #  <-- default lower priority variables for this role
        meta/             #
            main.yml      #  <-- role dependencies
        library/          # roles can also include custom modules
        module_utils/     # roles can also include custom module_utils
        lookup_plugins/   # or other types of plugins, like lookup in this case

    webtier/              # same kind of structure as "common" was above, done for the webtier role
    monitoring/           # ""
    fooapp/               # ""
```
- directory structure is important 
	- sub directories to the roles/ directory should follow the same name as the role its for 
		- yml files within the role directory can be for:
			- vars/
			- tasks/
			- handlers/
- /meta/main.yml
	- where you define dependencies (not tested on) 


```
roles/
    common/          # this hierarchy represents a "role"
        vars/
            main/    #  <-- variables associated with this role
                first_nested_directory/
                    first_variables_file.yml
                second_nested_directory/
                    second_variables_file.yml
                third_variables_file.yml
```

### Structure of a Role
#### Create Tasks
- created in /playbook/roles/tasks/main.yml
- when you create vars and tasks in roles, replaces 'playbook' structure 
	- the directory structure will replace the playbook?

### variables
- be aware of variable precedence 
- define variables within the role section of a playbook to override other values of the variable 
	- a way to force a variable value 




