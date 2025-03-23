---
tags:
  - ansible
  - ansible/roles
aliases: 
creation_date: 2025-03-21 20:46
last_updated: 2025-03-21 20:46
---
# Ansible Roles
### About
- purpose:
	- allows you to automatically load related vars, files, tasks, handlers, and other Ansible artifacts based on a known file structure 
- once content is grouped into roles, its easy to reuse it and share it with others
- a structured way to group related playbook content 


>[!question]- Why use Roles?
>Problem:
>- Playbooks can become large and difficult to maintain with duplicated tasks across playbooks. This makes managing playbooks and shared configurations amongst playbooks complex.
>
>Solution: Roles
>- Roles make playbooks more modular and easier to maintain 
>- Promotes re-use of tasks, variables, and handlers
>- Enhances collaboration across teams 
>- Improves readability and structure of playbooks
>


>[!summary]+
>- Roles improve organization, re-usability, and collaboration 
>- Role directory structure is important 
>- When you call a role, by default all tasks inside the role will run 
>	- Use `task_from` to only run specific tasks 
>- Three ways to call roles:
>	- `roles`
>	- `import_role`
>	- `include_role`






# 📁 Role Directory Structure
---
### About
- important 
- an Ansible role has a defined directory structure with 7 main standard directories 
	- must include at least 1 of these directories in each role 
	- can omit any directories the role does not use 


```
roles/
    common/               # this hierarchy represents a "role" (common is the role)
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
    monitoring/           # monitoring role
    fooapp/               # fooapp role 
```


>[!question] How does Ansible find relevant content for a given role?
>By default, Ansible will look in most role directories for a `main.yml` file for relevant content (also `main.yaml` and `main`)


##### Directories Explained
- `tasks/main.yml`
	- contains a list of tasks that the role provides to the play for execution 
	- use the `import_task: ` keyword with `import_role: ` in a playbook to use
- `handlers/main.yml`
	- handlers are imported into the parent play for use by the role or other roles and tasks in the play 
- `defaults/main.yml`
	- very low precedence values for variables provided by the role 
		- although these will take priority over other roles defaults, all other variable sources will override it
	- NOTE:
		- can include nested directories
- `vars/main.yml`
	- high precedence variables provided by the role to the play 
	- NOTE:
		- can include nested directories
- `files/stuff.txt`
	- files available for the role and it's children 
- `templates/something.j2`
	- templates to use in the role or child roles 
- `meta/main.yml`
	- metadata for the role 
	- includes:
		- dependencies 
		- optional Galaxy metadata (ex: platforms supported)
	- required for uploading into galaxy as a standalone role 
		- but not for use in own play 


>[!note]
>- None of the files above are required for a role 
>	- ex: can just provide `files/something.txt` or `vars/for_import.yml` and will still be valid role 
>- Variables from `vars/` and `defaults/` are imported into play scope unless disabled via the `public` option in `import_role` / `include_role` 


>[!question]- What is a 'stand alone' role?
>A role that is not part of a collection, but is individually installable content



---


## Storing and Finding Roles
- by default, Ansible looks for roles in the following locations 
	1. in collections 
		1. if using them
	2. in a directory called `roles/`, relative to the playbook file 
	3. in the configured `roles_path`
		1. default search path is `~/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles`
	4. in the directory where the playbook file is located 
- can also call a role with a fully qualified path

>[!note] If roles stored in different location, set the `roles_path` config option 
>- Done in `ansible.cfg`
>- Allows Ansible to find roles
>- Storing shared roles in a single location is easier to manage

###### To call a role with a fully qualified path
```yml
---
- hosts: webservers
  roles:
    - role: '/path/to/my/roles/common'
```



---






# ✨ Using Roles
---
## Methods in which you can use Roles
1. at play level with `roles` option 
	1. classic way of using roles in a play 
2. at tasks level with `include_role`
	1. allows you to re-use roles *dynamically* anywhere in the `tasks` section 
3. at task level with `import_role`
	1. allows you to re-use roles *statically* anywhere in the `tasks` section 
4. as a dependency of another role 
	1. using the `dependencies` keyword in `meta/main.yml` 

>[!question]+ How do you run **only one** specific task from a role?
>- Use the `task_from` parameter.
>
>Why
>- By default when you call a role using `roles`, `import_role` and/or `include_role`, ALL of the tasks inside the role are executed
>
>ex:
>```
>- name: Include the full role
> 	 include_role:
> 		 name: my_role
>```
>- this calls the entire role's task set from `my_role` 
>
>To only run a specific task from a role:
>```
>- name: Run specific task file from the role
>	include_role:
>		name: my_role
>		tasks_from: setup_firewall
>```
>- this would only run `roles/my_role/tasks/setup_firewall.yml`






---

#### `roles`: Using roles at Play level
```yml
---
- hosts: webservers
  roles:
    - common
    - webservers
```

When used at play level, each role 'x' looks for a `main.yml` (or `main.yaml` or `main`) in the following directories
- `roles/x/tasks/`
- `roles/x/handlers/`
- `roles/x/vars/`
- `roles/x/defaults/`
- `roles/x/meta/`


>[!note]+
>- If using `include_role` or `import_role`, can specify a custom file name instead of main.
>	- Only exception is the `meta` directory (does not allow for customization)


###### Can pass other keywords to the `roles` option
```yml
---
- hosts: webservers
  roles:
    - common
    - role: foo_app_instance
      vars:
        dir: '/opt/a'
        app_port: 5000
      tags: typeA
    - role: foo_app_instance
      vars:
        dir: '/opt/b'
        app_port: 5001
      tags: typeB
```


---


#### `include_role`: Dynamically use roles at Task level 
- included rules run in the order they are defined 
	- therefore, if there are other tasks before an `include_role` task, the other tasks will run first 
	- unlike `roles`
		- runs before any other tasks in a play 

```yaml
---
- hosts: webservers
  tasks:
    - name: Print a message
      ansible.builtin.debug:
        msg: "this task runs before the example role"

    - name: Include the example role
      include_role:
        name: example

    - name: Print a message
      ansible.builtin.debug:
        msg: "this task runs after the example role"
```


###### To pass other keywords when including a role:
```yaml
---
- hosts: webservers
  tasks:
    - name: Include the foo_app_instance role
      include_role:
        name: foo_app_instance
      vars:
        dir: '/opt/a'
        app_port: 5000
      tags: typeA
  # ...
```

###### To conditionally include a role:
```yaml
---
- hosts: webservers
  tasks:
    - name: Include the some_role role
      include_role:
        name: some_role
      when: "ansible_facts['os_family'] == 'RedHat'"
```

>[!warning]+
>`when` statement only works when roles are included dynamically with `include_role`
>- does NOT work with `import_role` or `roles` 


---


#### `import_role`: Statically use roles at Task level 
- behaviour is same as with using `roles` keyword 
	- NOT compatible with `when`
		- cannot use used with conditionals 
	- NOT compatible with looping 


```yaml
---
- hosts: webservers
  tasks:
    - name: Print a message
      ansible.builtin.debug:
        msg: "before we run our role"

    - name: Import the example role
      import_role:
        name: example

    - name: Print a message
      ansible.builtin.debug:
        msg: "after we ran our role"
```


###### To pass other keywords when importing roles
```yaml
---
- hosts: webservers
  tasks:
    - name: Import the foo_app_instance role
      import_role:
        name: foo_app_instance
      vars:
        dir: '/opt/a'
        app_port: 5000
  # ...
```




>[!question]+ What is the difference between `include_role` and `import_role`?
>`include_role` **(Dynamic)**
>- Allows for:
>	- looping
>	- conditionals (`when`)
>- Processed at: runtime
>	- when playbook is running
>	- role is loaded after all facts and variables are available 
>		- so Ansible can determine whether to run it or not 
>
>`import_role` **(Static)** 
>- Does NOT support:
>	- looping 
>	- conditionals
>- Processed at: parse time 
>	- before running anything
>	- role is loaded before any variables or facts are gathered
>	- role will always load, regardless 



---


### Role Argument Validation 
- when role argument specifications are defined, parameters supplied for the role are validated in order for the role to execute 
	- a new task is inserted at the beginning of role execution 
- specified in `meta/argument_specs.yml`


---


## Running a role Multiple times in one play 
- a given role only executes 1 time per play 
	- even if defined multiple times 
- UNLESS  
	- parameters defined on the role are different for each definition OR
	- set `allow_duplicates: true` 
		- in `meta/main.yml`

>[!question]+ Why do Roles only execute once per play?
>Because when you call a role, you are basically telling Ansible to execute all tasks built into the role.
>- therefore, if you were to call a role multiple times in a single play, you would be repeating the same set of tasks again 
>- uncommon where you would have to run a role multiple times in a single play

###### To use `allow_duplicates: true`
```yaml
# playbook.yml
---
- hosts: webservers
  roles:
    - foo
    - foo

# roles/foo/meta/main.yml
---
allow_duplicates: true
```



























# References
1. 