# git tips

## Config

### Level

3 different levels of configuration

 * _/etc/gitconfig_ : values for every user on the system
 ``` git config --list --system   ```
 * _~/.gitconfig_ : Specific to your user
  ``` git config --list --global   ```
 *  _.git/config_ config file in the git directory : Specific to that single repository
 ``` git config --list   ```


### credentials


#### credential helper

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

*permanently*

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

```sh
#  to add only modified files already in source control 
git add -u 

```


## Info


### diff

```sh
git --no-pager  diff # workspace vs HEAD
git --no-pager  diff --cached # index vs HEAD
git --no-pager  diff HEAD # workspace & index vs HEAD
git --no-pager  diff HEAD~ HEAD VERSION # show difference between two commits of a specific file use : git diff old-sha1 new-sha1 file
```

### diff using an external tool

```sh
git difftool -y [-t ext-diff-tool]
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
```


git show 
cat .git/$(cat .git/HEAD| cut -f2 -d' ')


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
