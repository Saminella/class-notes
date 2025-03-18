---
class: 
topics: 
creation_date: 2025-01-14 17:28
last_updated: 2025-01-14 17:28
---
#### To Do
- [ ]
---
# Git Cheat Sheet

#### To reset git connection 
- delete the .git file 


---

# Basic Git Operations 
### git stage 
- About 
	- git stage (or git add) prepares changes for a commit by adding them to the 'staging area' 
		- the staging area is between your local working directory (where you make changes) and your repository (where your history and commits live)
- When to use
	- after modifying a file, stage it for commit 
- Syntax
	- git add modified_file.txt
	- NOTE
		- can add entire directories BUT cannot contain another repository nested inside 
			- remove .git file of nested directory if this is the case
		- to add the entire root directory: git add .

### git commit 
- About 
	- takes everything from the staging area and adds it to your repository
	- a ==local== operation 
		- only affects local repository 
		- does not interact with remote repository until changes are _pushed_ 
- When to use
	- commit often (but push selectively)
	- each commit should represent a logical unit of work 
	- when you commit often it makes it easier to understand changes and revert them if necessary
- Syntax
	- git commit -m "message about changes"

### git push 
- About 
	- uploads your committed changes to a remote repository (Github) 
	- a network operation 
		- transfers commits from local repository to remote repository 
- When to use
	- push changes when they are ready to be shared or when you need to collaborate with others 
- Syntax
	- git push origin main 
		- // _main_ refers to the main branch. Tells git to push commits from local _main_ branch to remote _main_ branch
		- // _origin_ is short for the URL of your remote repo
			- an alias for the URL so you don't have to type something like https://github.com/your-username/your-repo.git 




---



# Branches
### To check your current branch
- Enter:
	- git branch 
- The branch with the asterisk (\*) is the current branch 

### To switch to a different branch 
- Enter:
	- git checkout \<branch name>

### To merge changes of a different branch into the branch you are currently working in
- Enter:
	- git merge \<other branch>





## Git Merge
---
```
git checkout <destination branch>
git merge <source branch> 
```
- have to be in the branch you would like to update with the source branch content 
	- ex: if you want to add stuff from the `main` branch to the `linux` branch
		- `git checkout linux`
		- `git merge main` 





### To reconcile divergent branches
First run:
```
git status
```
 Fetch latest changes
```
git fetch origin
```

Rebase (look into)
```
git rebase origin/main
```

Now push
```
git push origin main
```


#### To check if a directory is initialized as a Git repository 
- execute: 
	- ls -a
- if the output contains a .git folder, the directory is a Git repository 
	- if not, it is NOT
![[Pasted image 20250114173006.png]]





## Helpful Resources
- https://graphite.dev/guides/git-commit-vs-push
	- explains difference between git add, git commit, and git push




---






# Tagging
- purpose
	- tags are used to point to a specific place in a repository's history 
- examples
	- usually used to mark release points 

#### To list the existing tags 
```git
git tag
```
- the -l or --list option 

#### To create tags 
##### lightweight tags
- like a branch

##### annotated tags
- stored as full objects in the Git database 
- contain more metadata 




---






# Random Tips
- git does not track empty folders!! 
	- therefore if updating an empty folder it will not show up 