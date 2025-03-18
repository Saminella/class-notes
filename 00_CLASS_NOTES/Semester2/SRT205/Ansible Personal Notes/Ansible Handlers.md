---
tags:
  - ansible/handlers
  - ansible
aliases:
  - Handlers
creation_date: 2025-03-11 11:38
last_updated: 2025-03-11 11:38
---
# Ansible Handlers





# About Handlers From Textbook
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















# References
1. 