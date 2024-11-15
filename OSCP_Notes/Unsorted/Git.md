There's [git-dumper](https://github.com/arthaud/git-dumper)

When we find a git directory on a website we can download it with:
- `wget -r http://site.com/.git` 
	- OR `git-dumper http://site.com/.git folder.git`
		- may need `pipx install git-dumper` first
	- OR simply `git clone http://site.com/.git`
- Then run `git checkout` inside the directory
- 

### command guide

`git clone`: Clone the repository to your local machine.
	- `git clone <repository_url>`

`git log`: View the commit history to understand the evolution of the repository.
	- `git log`

`git status`: Check the current status of the repository, including any modified or untracked files.
	- `git status`

`git diff`: View the differences between files, useful for understanding changes made between commits.
- `git diff`

`git branch`: List all branches in the repository.
	- `git branch -a`

`git show`: Show information about a specific commit.
	- `git show <commit_hash>` (967fa71c359fffcbeb7e2b72b27a321612e3ad11)

`git blame`: See who last modified each line of a file, helpful for understanding the history of changes.
	- `git blame <file_name>`

`git grep`: Search for specific strings or patterns within the repository.\
	- `git grep <search_term>`

`git remote`: View the remote repositories associated with the local repository.
	-`git remote -v`

`git reflog`: Show a log of changes to the repository's HEAD.
	- `git reflog`

`git fsck`: Perform a filesystem check on the repository.
	- `git fsck`