---
tags:
  - ansible
  - playbook
aliases: 
creation_date: 2025-03-07 14:51
last_updated: 2025-03-07 14:53
---
# Playbook
### About Playbooks
- written in YAML 
- a playbook is composed of one or more 'plays' in an ordered list 
	- each play executes a part of the overall goal of the playbook 
- playbooks run from top to bottom 
	- individual plays themselves also execute from top to bottom 




```YAML
---
- name: Name of Playbook
  hosts: hosts affected

  tasks: 
  - name: name of play
    module: 
```





# Examples
---

### Basic Playbook Examples
```yml
---
- name: Update web servers
  hosts: webservers
  remote_user: root # user account for SSH connection

  tasks:
  - name: Ensure apache is at the latest version
    ansible.builtin.yum:
      name: httpd
      state: latest

  - name: Write the apache config file
    ansible.builtin.template:
      src: /srv/httpd.j2
      dest: /etc/httpd.conf

- name: Update db servers
  hosts: databases
  remote_user: root

  tasks:
  - name: Ensure postgresql is at the latest version
    ansible.builtin.yum:
      name: postgresql
      state: latest

  - name: Ensure that postgresql is started
    ansible.builtin.service:
      name: postgresql
      state: started
```






# Blocks 
---










# References
1. 