---
class: srt210
topics: 
tags:
  - samba
  - file_sharing
  - permissions
creation_date: 2025-02-11 08:04
last_updated: 2025-02-11 08:04
---
#### To Do
- [ ] #lab due tomorrow
---
# SRT210_Lec5

### shared protocols


### Inter OS operability 
- limitations are mainly on the client side 
	- client has to have SSH service to be able to access file sharing service (usually)
- some protocols are designed for interoperability 
	- others are not and you need to consider

#### Client machines
- usually client machines in a network are different
	- different OS's main thing to be concerned about 
- have to be able to provide services to all client devices, regardless of their OS 


# SAMBA
- used to 
- directories on a linux server that are available to clients 
	- including Windows 
	- other file sharing services only work with linux clients
	- "Samba runs on Unix platforms, but speaks to Windows clients like a native" 
- SAMBA is the linux version of [[Server Message Block]] 
- consult man pages 
- SMBD
	- samba daemon 
	- important look 
- a 'newer' service
	- therefore, accepts different styles of comments
	- BUT way better if you stick to one style of comments 
		- ex: \# or //


## Samba Sections
- define sections and within the sections define the parameters
	- each section controls a particular folder, printer etc..
- similar to DNS when you put parameters inside zones 
- each section represents one thing you want to share on the network 
	- and determines how this thing can be shared
		- ex: who can access, when, how etc..
- defined in smb.conf

#### Special sections
- global
	- affects the server as a whole
	- ex: a welcome message 
- homes
	- allows to automatically create mounts for user's home directories 
	- look up
- printers

## SAMBA parameters
- defined within 'sections'
	- some parameters can only be applied to global or to sections so refer to man pages 
		- will be indicated by (G)lobal and (S)ection
- syntax:
	- name = value 
	- whitespace is ignored 
	- not that particular about case 
		- case insensitive 
	- many parameters use **separate** words (instead of dashes or underscores between words)
		- ex: allow insecure wide links = no 
	- although many different ways to wright Boolean values, stick to one
- some parameters are 'synonyms' for other ones
	- for ease of use
	- will be specified in man pages 
	- make sure not to write both synonyms which will cause conflict
- smb.conf 
	- main config file for Samba
	- man page: https://www.samba.org/samba/docs/4.9/man-html/smb.conf.5.html


#### Parameters (better to refer to man pages)
.workgroup \<string>
- not explored in this lab but might appear in assignment
.server string \<string>

.smb passwd file \<string>
- have to keep separate files for samba passwords and ssh 
.path 
- important
- specify absolute path to folder you want to hare 

.public
- if you need account to access service
- important security setting 

.writable
- once user accesses share, can they create/edit files etc.. 
.printable
- difficult to control on client end 
- can just copy andpast into something printable

.create mask 

.host allow 
- specify addresses of machines that can access a specific share

.valid users 
- what user accounts can access the share 





## Samba Password
- have to actually create samba accounts using smb password
	- ex: smbpasswd -La anne.admin
		- creates user and password 
- Samba passwords are separate from regular login password 
- use this account to login using the smbclient


#### To create a Samba account
```bash
smbpasswd -La anne.admin
```



## Samba Config
- /etc/samba/smb.conf 
	- main conf file 

```
[global]  # global section
workgroup = WORKGROUP
hosts allow = 192.168.157.0/24
public = no

[demo]  # custom defined section
path = /demosamba
read only = no
// could also do writable = yes
```
ABOUT
```
workgroup = WORKGROUP
```
- sets the current workgroup (or domain) which the Samba server will advertise itself 
- workgroups are really just NetBIOS group names 
	- look into NetBIOS
- naming
	- follow NetBIOS naming convention 
	- avoid:
		- same name as server or user
			- may lead to problems with WINS name resolution 
```
path = /demosamba
```
- path 
	- specifies the path to the shared folder for the 'demo' section
	- should reflect the actual location on the Samba server's filesystem 








#### To restart 
```bash 
sudo systemctl restart smbd
```
NOTE
- don't have to restart service after making changes
	- BUT double check
	- recommended to restart because can cause problems 






## Client config
NOTE:
- path to share name is not the actual path to the folder on the server
	- it is the the same as defined in the smb.conf file 
### For Linux clients
##### cli 
```bash
smbclient host -U username
```
- smbclient is the command line tool used to connect and interact with a shared samba folder
- Linux works with \ or / 
- once you login as a user and the prompt has changed, can enter 'help' to see all the commands available within the samba shared folder

EXAMPLES:
```bash
smbclient '\\192.168.15.1\demo' -U anne.admin
```
- will connect to the \demo shared folder in the 192.168.15.1 Samba server as the anne.admin user account
- once you login as a user the prompt will change 

```bash
smbclient -L '\\192.168.15.1' -U anne.admin
```
- the -L option will 'List' all the shared folders available in the 192.168.15.1 server when logged in as anne.admin 
![[Pasted image 20250211105754.png]]


#### gui 
- https://ubuntu.com/tutorials/install-and-configure-samba#4-setting-up-user-accounts-and-connecting-to-share 
- navigate to File Manager > Connect to Server (or similar option) > enter server address in format 'smb://[server IP]/[share name]'



### For Windows Clients
- depends on your level of access
- can simply add a samba share as a new drive letter 
	- choosing S:\ is good because indicates its 'Samba' 
	- ![[Pasted image 20250211105734.png]]
- in the file exporer search bar
	- smb://externalIPof host\remote
	- ex: smb://a.b.c.d/sharename 









## Mounting 
- when you 'mount' the shared folder hosted on the samba server on a client 
	- so you don't have to login using smbclient every time you want to access the 
- ex: if you have a client machine and you want to connect a folder on the client machine to the samba server 

```bash
mount -t cifs //192.168.15.1/demo /remote -o username=anne.admin
```
- do this on the client 

# Troubleshooting
### testparm
- will go over config files and tell you if there is errors
- looks at config files even when samba running 
- simply enter 'testparm' 
	- don't need to specify any parameters or destinations 





# Questions
- what ports does Samba use?
- forward chain partial example
	- iifname ens33 oifname ens37 
- go over passwords again
	- separate for os login and samba? or ssh and samba 
- can you restrict certain commands based on user 
	- like when you enter 'help' as a particular user, can you restrict some commands so they are not all available 