---
tags:
  - ansible
  - ansible/module
aliases:
  - Modules
creation_date: 2025-03-07 15:06
last_updated: 2025-03-07 15:06
---
# Modules




# Common Modules 
##### apt = install packages




##### ansible.builtin.service = restart service


###### Example:
```yml
- name: Restart nginx service
  service:
    name: nginx
    state: restarted
  when: 
```


### find 
- returns a list of files based on specific criteria 










# References
1. 