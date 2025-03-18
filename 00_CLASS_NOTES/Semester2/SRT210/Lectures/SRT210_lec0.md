
#### To Do
- [x] #lab Lab 1  [due:: 2025-01-14]  [completion:: 2025-01-14]

# Sudo 

#### Why use sudo 
- even though its more convenient to use sudo -i or to use su...
	- in workplace setting you will likely not be given direct access to root account 
		- therefore cannot use su, can only use sudo 
	- when logged in as root using su, all actions are logged under the root account 
		- if multiple people use the same root account, cannot tell who's activity is what 
		- difficult to track malicious activity 
- benefits of using sudo 
	- can still execute commands with elevated privileges using sudo BUT actions are still logged with the user (not root)
		- very important for security and auditing 
		- the sudo logs will be associated with the user but will be stored in different place than the users regular logs
	- don't have to give out root password to lots of people 
	- by using sudo, can apply granular permissions 
		- can allow/prevent specific commands for a user in the /etc/sudoers.d config file(not root)
		- very important for security and auditing 
		- the sudo logs will be associated with the user but will be stored in different place than the users regular logs
	- don't have to give out root password to lots of people 
	- by using sudo, can apply granular permissions 
		- can allow/prevent specific commands for a user in the /etc/sudoers.d config file

### The Wheel/Sudo Group 
- there is a special group already defined with access to sudo to run all commands as root 
	- on RHEL = Wheel
	- on Ubuntu = sudo 
- adding a user to this group will allow them to use sudo to run any command 

#### How does sudo work
- for a user to be able to use 'sudo' have to be added to the sudo group 
- all actions made using sudo are logged to the user's account, NOT the root account 
	- when su is used, actions are logged to the root account 
	- when you check journalctl can see that a what users used sudo to execute what accounts 
		- can also see what accounts tried to use sudo but failed 


#### Allowing Sudo Access
- /etc/sudoers 
	- controls who can use sudo command and what commands they can execute 
		- important, because with su you can use all commands BUT with sudo you can apply granular controls that prevent specific users from using 
	- if there is a . anywhere in the file name this directory will ignore it
- /etc/sudoers.d/
	- all files in this directory will be automatically added to the /etc/sudoers file 
	- much easier to manage 
	- format:
		- WHO WHERE=(AS_WHOM) WHAT 
			- WHO
				- when start  with % means a group, otherwise it applies to a specific user 
			- WHERE
				- the systems (machines) this entry applies to 
					- ex: FQDN, host name, etc.. 
				- can limit a users privilege to specific machines 
			- AS_WHOM
				- what accounts you are allowed to become 
				- usually root, but sometimes can be a different regular user 
				- usually either ALL or ROOT for this course
			- WHAT
				- comma separated list of the absolute paths to the commands this user can run as other users 
				- can include options and arguments as well to further restrict
				- to explicitly prevent commands 
					- include ! before the absolute path to the command 
					- ex: !/usr/bin/su
						- // this is very common and a good practice to prevent unauthorized su use
- to reload:
	- ==sudo systemctl daemon-reload ==












