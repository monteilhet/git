# git tips

## Config

### Levels of config

3 different levels of configuration

* _/etc/gitconfig_ : values for every user on the system
 ```git config --list --system```
* _~/.gitconfig_ : Specific to your user
  ```git config --list --global```
* _.git/config_ config file in the git directory : Specific to that single repository
 ```git config --list```


### Credentials

#### Credential helper


Turn on the credential helper so that Git will save your password in memory for some time.

```sh
# Set git to use the credential memory cache
# By default, Git will cache your password for 15 minutes.
git config --global credential.helper cache

# Set the cache to timeout after 1 hour
git config --global credential.helper 'cache --timeout=3600'


# or defined permanently
git config credential.helper store

```


### Pager 

#### Turn off pager (used by log, diff, list)

permanently

```sh
git config --global core.pager ''
```

or temporary using --no-pager option for git (=> before subcommand)

```sh
git --no-pager log --oneline
```

restore initial setting

```sh
git config --global --unset core.pager
```

#### Use most as pager

```sh
git config --global core.pager most
```

### Merge tool

#### Using graphical external tool

*temporary* to launch graphical diff tool instead of the default diff 

```sh
# -y no prompt option
git difftool -y -t meld
```

*permanently* configure merge tool

```sh
git config --global merge.tool meld
```

then use mergetool to resolve conflicts

```sh
git mergetool -y
```

#### Remove the backup .orig

To automatically remove the backup .orig as files are successfully merged by git mergetool set mergetool.keepBackup to false

```sh
git config --global mergetool.keepBackup false
```


## Basics

### Setup

```sh
#  clone the repository specified by <repo>
git clone <repo>

```

### Staging

```sh
# to add only modified files already in source control 
git add -u 

# to add all modified files in index 
git add .  # <=> git stage

# to unstage file reset [--mixed]
git reset file

# to unstage all modified files added to index
git reset

# to remove file from index
git rm --cached file

# to remove file from index & workspace
git rm file

# revert change in index & workspace
git checkout HEAD file

# revert change in workspace using index
git checkout file

```
### Commiting

```sh

# commit changes staged in index
# -v  commit verbosely, i.e. includes the diff of the contents being committed in the commit message screen
git commit -v

# -m specify directly commit messagel
git commit -m 'commit msg'

#  automatically stage every file that is already tracked before doing the commit 
git commit -a -m 'commit msg'

# bypass pre-commit hook
git commit --no-verify -m 'commit msg'

```

### Branching

```sh
# list all local branches
git branch

# list all remote branches
git branch -r

# list all local and remote branches
git branch -a

# create a new branch named <branch>, referencing the same point in history as the current branch
git branch <branch>

# create a new branch <new> referencing <start-point>, and check it out.
git checkout -b <new> <start-point>


```

## Alias

```
ci = commit
co = checkout
dc = diff --cached
df = diff
head = !git --no-pager show -s --pretty='tformat:%h (%s, %ad)' --date=short
kc = difftool -y --cached
kf = difftool -y
last = !git --no-pager log -1 HEAD
lg = log --oneline --graph
lol = log --graph --decorate --pretty=oneline --abbrev-commit
rv = checkout HEAD --
st = status
us = reset HEAD
```

## Info


### diff

```sh
git --no-pager  diff # workspace vs index
git --no-pager  diff --cached # index vs HEAD
git --no-pager  diff HEAD # workspace & index vs HEAD
git --no-pager  diff HEAD~ HEAD VERSION # show difference between two commits of a specific file use : git diff old-sha1 new-sha1 file
```

> --name-only can be used to show only files changed
```sh
# differences between two branches :  all commits in experiment that aren’t in master 
git diff master..experiment
# differences between a local branch and a remote branch : show all commits on remote branch not in local branch
git diff master..origin/master
```

### diff using an external tool

NB by default use external tool defined by merge.tool setting

```sh
git difftool -y [-t ext-diff-tool]
git mergetool -y [-t ext-tool]
```

### HEAD pointer 

```sh
git symbolic-ref HEAD
# <=>
cat .git/HEAD | cut -f2 -d' '
```

### retrieve HEAD commit id

```bash
git rev-parse --verify HEAD
# <=> 
cat .git/$(git symbolic-ref HEAD)
cat .git/$(cat .git/HEAD| cut -f2 -d' ')
```

### Check if a commit exist

```bash
git cat-file -e <commit-id>
```


## Remote


### Tags

sharing tags
deleting tags


## Stashing

```bash
# save your local modifications to a new stash
git stash
# list all current stashes
git --no-pager stash list
# show the list of changes in the first stash
git stash show stash@{0}
# display patch of a specified stash
git stash show stash@{0} -p
# <=>
git difftool stash@{0}
# ~ <=>
git show stash@{0} --oneline

# show diff using difftool for all files in stash
git difftool stash@{0}
# show diff using difftool for the specified file in stash
git difftool stash@{0} -- <filename>

# restore the changes from the most recent stash, and remove it from the stack of stashed changes
git stash pop

# drop the stash
git stash drop [<stash-ref>]

```

## Alias

Common aliases

```
ci = commit
co = checkout
br = branch
dc = diff --cached
df = diff
head = !git --no-pager show -s --pretty='tformat:%h (%s, %ad)' --date=short
kc = difftool -y --cached
kf = difftool -y
last = !git --no-pager log -1 HEAD --stat
lg = log --oneline --graph
lol = log --graph --decorate --pretty=oneline --abbrev-commit
rv = checkout HEAD --
st = status
us = reset HEAD
k = !gitk --all&
```

## submodule


```bash
git submodule add url dir
```


=> add entry in .gitmodules
[submodule "dir"]
	path = dir
	url = https://github.com/puppetlabs/puppetlabs-vcsrepo

```bash
cat .gitmodules


git submodule init
git submodule update [--init]

git submodule status
```


## Plumbing


git show --format=raw

```bash
# display content of file e.g. README in git index
git show :README
# <=> 
git cat-file blob $(git ls-files -s README | awk '{print $2}')  # extract sha1 of README in index
git show $(git ls-files -s README | awk '{print $2}')
```


### Tips to retrieve last file content added to index

```bash
objectid=$((find .git/objects -type f -print0 | xargs -0 ls -t | head -n 1) 2> /dev/null | awk -F/ '{ print $3 $4 }')

# to list last created files in object store
# find .git/objects -type f -cmin -60 | awk -F/ '{ print $3 $4 }'

# alternative to extract object id
# a=$((find .git/objects -type f -print0 | xargs -0 ls -t | head -n 1) 2> /dev/null)
# id=$(echo ${a#.git/objects/} | sed s#/##)

git-cat file -p $objectid

```