---
class: srt205
textbook: "ansible: up and running 3rd edition"
chapters: "3"
tags: 
creation_date: 2025-03-08 11:51
last_updated: 2025-03-08 11:51
---
#### To Do
- [ ]
---
# 3 Playbooks A Beginning

### What is a Playbook
- a playbook is the term that Ansible uses for a configuration management script 


### File Management Convention Example
```bash
.
├── Vagrantfile
├── ansible.cfg
├── files
│   ├── index.html
│   ├── nginx.conf
│   ├── nginx.crt
│   └── nginx.key
├── inventory
│   └── vagrant.ini
├── requirements.txt
├── templates
│   ├── index.html.j2
│   └── nginx.conf.j2
├── webservers-tls.yml
├── webservers.yml
└── webservers2.yml
```
- 'files' contains all files that are copied from control node to managed nodes 
- 'templates' contains jinja2 templates 


# Pages 35 - 50 
---

### To Execute a Playbook 
```
ansible-playbook -i <inventory-file> <playbook.yml>
```





# Playbooks are YAML
---

### Lists
###### YAML example: 
```
shows:
   - My Fair Lady
   - Oklahoma
   - The Pirates of Penzance
```


###### JSON example: 
```
{
   "shows": [
     "My Fair Lady",
     "Oklahoma",
     "The Pirates of Penzance"
     ]
}
```


---

### Dictionaries 
###### YAML example:
```
address:
  street: Main Street
  appt: 742
  city: Logan
  state: Ohio
```


###### JSON example:
```
{
  "address": {
    "street": "Main Street",
    "appt": 742,
    "city": "Logan",
    "state": "Ohio"
  }
}
```




# Anatomy of a Playbook
---
- the structure of a playbook is it is a **_list of dictionaries_** 
	- list of 'plays' 
- a playbook contains one or more plays 
	- a 'play' associates an un-ordered set of hosts with an ordered list of tasks
		- each task is associated with exactly 1 module 


![[Pasted image 20250309132753.png]]


###### Example playbook:
```

- name: Configure webserver with nginx
  hosts: webservers
  become: true

  tasks:
    - name: Ensure nginx is installed
      package:
        name: nginx
        update_cache: true

    - name: Copy nginx config file
      copy:
        src: nginx.conf
        dest: /etc/nginx/sites-available/default

    - name: Enable configuration
      file:
        src: /etc/nginx/sites-available/default
        dest: /etc/nginx/sites-enabled/default
        state: link

    - name: Copy index.html
      template:
        src: index.html.j2
        dest: /usr/share/nginx/html/index.html

    - name: Restart nginx
      service:
        name: nginx
        state: restarted
...
```
- each 'play' most contain the `hosts` variable 
	- can be a single host, group, or expression of a set of hosts to configure 
- in this example there are:
	- 1 play that has 5 tasks 




# Modules
---
- modules are scripts that come packaged with Ansible and perform some kind of action on a host 




# Variables
---
- variables are defined in the `vars:` section of a playbook 
- where to use variables
	- in tasks and template files 
- variables are referenced using `{{ variable }}`

###### Example: how to define variables:
```
vars:
  tls_dir: /etc/nginx/ssl/
  key_file: nginx.key
  cert_file: nginx.crt
  conf_file: /etc/nginx/sites-available/default
  server_name: localhost
```

###### Example: how to reference variables in a play:
```
- name: Manage nginx config template
  template:
    src: nginx.conf.j2
    dest: "{{ conf_file }}"
    mode: '0644'
  notify: Restart nginx 
```


### Variable exceptions 
- problems arise when:
	- you reference a variable right after specifying the module 
		- Ansible will try to parse it as a dictionary instead of a string 
		- therefore MUST quote the arguments 
	- your argument contains a colon 

###### Example: BAD = will produce error:
```
- name: Perform some task
  command: {{ myapp }} -a foo
```


###### Example: GOOD = will not produce error
```
- name: Perform some task
  command: "{{ myapp }} -a foo"
```





# Loop
---
- when you want to run a task with each item from a list


###### Example: basic loop with variables
```yml
- name: Copy TLS files
  copy:
    src: "{{ item }}"
    dest: "{{ tls_dir }}"
    mode: '0600'
  loop:
    - "{{ key_file }}"
    - "{{ cert_file }}"
  notify: Restart nginx
```
- the task is executed multiple times
	- each iteration, `item` is replaced by different values from the specified 'loop' list 
- NOTE:
	- `{{ item }}` is a keyword used in loops 
		- `item` is the only variable that is replaced by values in the `loop` list 
		- other variables like `{{ tls_dir }}` are not replace because Ansible does not recognize it as a keyword






# Handlers
---
- one of the conditional forms supported by Ansible 
- similar to a task, but runs ONLY if it has been notified by a task 
	- a task will fire the notification if Ansible recognizes that the task has changed the state of the system 
- handlers usually run at the end of the play 
	- after all tasks have run 
	- BUT can force a handler to run in the middle of a play (see example below)
- if a play contains multiple handlers:
	- handlers always run in the order they are defined in the HANDLERS section NOT notification order 
	- only run once, even if notified multiple times 
- common uses for handlers:
	- reboots and restarting services 
- testing
	- best to test using `uri` module
	- because testing a playbook with handlers can result in weird behavior 


###### Example: 'handlers' section 
```yml
handlers:
  - name: Restart nginx  # notify statement refers to this name 
    service:
      name: nginx
      state: restarted
```

###### Example: how the handler is notified by a task
```yml
- name: Manage nginx config template
  template:
    src: nginx.conf.j2
    dest: "{{ conf_file }}"
    mode: '0644'
  notify: Restart nginx # notifies the handler
```
- the task notifies the handler by passing the handler's name as the argument 
	- `Restart nginx` is the handler name 


###### Example: how to force notify a handler in the middle of a play
```yml
- name: Restart nginx
  meta: flush_handlers
```









# Examples
---
### Complete Example from Textbook:
```yml
---
- name: Configure webserver with Nginx and TLS
  hosts: webservers
  become: true
  gather_facts: false

  vars:
    tls_dir: /etc/nginx/ssl/
    key_file: nginx.key
    cert_file: nginx.crt
    conf_file: /etc/nginx/sites-available/default
    server_name: localhost

  handlers:
    - name: Restart nginx
      service:
        name: nginx
        state: restarted

  tasks:
    - name: Ensure nginx is installed
      package:
        name: nginx
        update_cache: true
      notify: Restart nginx

    - name: Create directories for TLS certificates
      file:
        path: "{{ tls_dir }}"
        state: directory
        mode: '0750'
      notify: Restart nginx

    - name: Copy TLS files
      copy:
        src: "{{ item }}"
        dest: "{{ tls_dir }}"
        mode: '0600'
      loop:
        - "{{ key_file }}"
        - "{{ cert_file }}"
      notify: Restart nginx

    - name: Manage nginx config template
      template:
        src: nginx.conf.j2
        dest: "{{ conf_file }}"
        mode: '0644'
      notify: Restart nginx

    - name: Enable configuration
      file:
        src: /etc/nginx/sites-available/default
        dest: /etc/nginx/sites-enabled/default
        state: link

    - name: Install home page
      template:
        src: index.html.j2
        dest: /usr/share/nginx/html/index.html
        mode: '0644'

    - name: Restart nginx
      meta: flush_handlers

    - name: "Test it! https://localhost:8443/index.html"
      delegate_to: localhost
      become: false
      uri:
        url: 'https://localhost:8443/index.html'
        validate_certs: false
        return_content: true
      register: this
      failed_when: "'Running on ' not in this.content"
      tags:
        - test
...
```











