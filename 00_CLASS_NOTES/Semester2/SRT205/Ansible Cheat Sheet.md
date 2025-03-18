---
class: 
topics: 
tags: 
creation_date: 2025-01-25 15:35
last_updated: 2025-01-25 15:35
---
#### To Do
- [ ]
---
# Ansible Cheat Sheet

### OpenSSH Setup 
- to install on client 
- to install on server
- to copy ssh public key to managed nodes
	- ssh-copy-id username@remotehost 
	- will automatically append public key of control node to ~/.ssh/authorized_keys or will create new file 
	- ex:
		- ssh-copy-id smiki@192.168.0.2


What are 'inventory' files used for?

## What are 'playbook' files used for?
- yaml files 
- define a series of tasks to be executed on one or more managed nodes 
- like a script that runs modules to perform specific tasks and defines how these tasks should be executed
- each 'task' in a playbook calls a specific module 
	- ex: apt, copy, template, etc..
- executed from top to bottom 
	- all tasks run in order they are defined 
- to run particular parts of a playbook can use:
	- ansible-playbook playbook.yml --start-at-task="task to start at"

#### structure:
- plays
	- a 'play' is a section of the playbook 
	- targets a specific set of hosts and describes the tasks to run 
	- can have multiple in a single playbook, each targeting different groups of hosts 
- tasks
	- each 'play' contains 'tasks' 
	- a task defines:
		- the module to use
		- parameters for the module 
		- a name of easy identification 
	- each task can use a different modules 
	- can have as many tasks in a playbook 
- variables
	- can define variables to make playbooks more dynamic and reusable 
- handlers
	- special tasks triggered by other tasks 
	- use cases
		- for actions like restarting services after a change 

#### Playbook Example
```yaml
---
- name: Set up a web server # name of 'play' 
  hosts: web_servers # the group of hosts this play applies to (defined in inventory file)
  become: yes  # Enable privilege escalation
  vars: # defines a variable
    html_content: "<h1>Welcome to My Web Server!</h1>"

  tasks:
    # Ensure the system is updated
    # Use apt module to refresh the package cache 
    - name: Update apt cache
      apt:
        update_cache: yes

    # Install nginx
    # Ensure the nginx package is installed
    - name: Install nginx
      apt:
        name: nginx
        state: present

    # Ensure nginx is running
    - name: Start and enable nginx
      service:
        name: nginx
        state: started
        enabled: yes

    # Create a custom HTML file
    # uses copy module to create custom index.html file in web server's root directory
    - name: Create index.html
      copy:
        content: "{{ html_content }}"
        dest: /var/www/html/index.html

    # Restart nginx to apply changes (if needed)
    - name: Restart nginx
      service:
        name: nginx
        state: restarted

```
#### To run a playbook
- ansible-playbook -i static_inventory playbook.yml

What language are inventory files written in?
- ini 
- or yaml??

What different ways can you execute modules?
- directly use the ansible command (ad-hoc)
- inside a playbook 

#### How do modules work?
- tasks are the jobs to be completed, modules are the tools necessary to complete the jobs 
- task defines an action that needs to be done and the modules executes on a managed host to accomplish that action 

#### What is a Task
- defines the action to be applied to a managed host
- does not specify the host it will be applied to 
- can invoke a module within a task to be executed on a host, but that host will be defined outside of the task, at the play level 

#### What is a Play 
- includes a list of tasks and maps that list to specific managed hosts 
- can contain variables and may contain role 

#### What is an Ansible Role
- packages Ansible content for use inside of a play 
	- includes:
		- tasks, handlers, variables, plugins, templates, and files 
- can be imported into a play, reused in other plays/playbooks, shared for use by other automation users 



