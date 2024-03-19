---
title: Git 101
date: 2019-02-01
lastmod: 2022-07-04
categories:
- Tech
tags:
- CS
- Tool
---

Git Tutorial

<!--more-->

---

# Git Basics

## Status

Git has three main states that your files can reside in: **committed**, **modified**, and **staged**:

- **Committed** means that the data is safely stored in your local database.
- **Modified** means that you have changed the file but have not committed it to your database yet.
- **Staged** means that you have marked a modified file in its current version to go into your next commit snapshot.

![Git States](https://git-scm.com/book/en/v2/images/areas.png)

## Snapshot

- A commit records a snapshot of all the (tracked) files, each with a commit id
- Branch is a lightweight movable pointer to one of these commits
- HEAD is pointing to the current branch you are working on. Or to a specific commit (detached mode)
- Relative commit refs: `HEAD^`, `HEAD^^`, `HEAD~3`

# Git Basic Operation

## Initialize Repo

```bash
cd my_project
git init
git add .
git commit -m "message"
```

## Check Status

```bash
git status
```

This will tell you files that are untracked (new file) and tracked but not staged (modified file). `git add` should be used for both cases. After that `git status` will show "changes to be committed" and we can use `git commit -m "message"` to safely commit the change.

```bash
$ git status -s
 M README            # modified but not staged
MM Rakefile          # both staged and unstaged
A  lib/git.rb        # new files in staging area
M  lib/simplegit.rb  # modified and staged
?? LICENSE.txt       # new files that aren't tracked
```

## View Changes

```bash
# compared with current saved version
git diff
git diff HEAD

# compare with previous saved version
git diff HEAD~1

# compare stage with current saved version
git diff --staged

# see the difference for a particular file
git diff <file>

# compare between two commits
git diff HEAD~3..HEAD

# compare between two branches
git diff <branch1>..<branch2>
```

## Stash Files

We can stash our current work and move to another job, say debugging. After finishing the job we can continue on our previous work.

```bash
# stash current work
git stash

# list the stashed work
git stash list

# restore ith stashed work
git stash apply stash@{i} # still keep the stashed content
git stash drop stash@{i}  # drop the stashed content

git stash pop stash@{i}   # = apply + drop
```

## Commit Changes

```bash
git commit -m "message"

# skipping the staging area
git commit -am "message"
```

## View Commit History

```bash
git log

# show the difference introduced in each commit
git log -p

# show only the latest two entries
git log -p -2

# abbreviated stats
git log --stat

# change output format
git log --pretty=oneline
git log --pretty=short
git log --pretty=full
git log --pretty=fuller

# specify your own output format
git log --pretty=format:"%h - %an, %ar : %s"

# graph
git log --pretty=oneline --graph

# limit output
git log --since=2.weeks
git log --author=silencial
git log --grep=message

# specify changes
git log -S function_name
```

## Remove Files

Remove the file from both the staging area and the working directory.

```bash
# remove the file from both the staging area and the working directory
git rm

# remove the file from staging area but keep in the working directory
git rm --cached
```

## Rename Files

```bash
git mv <file> <new_file>
```

Git is case insensitive. Use `git mv` if you want to change the case of the filename.

## Tag & Describe

tags are anchors that mark commits

```bash
# list tags
git tag # list all
git tag -l "v1.0" # match pattern

# create tags
git tag <name> <commit>
git tag -a <name> -m "message" # with message

# show information of a tag
git show <tag name>

# push tags to remote
git push origin <tag name>
git push origin --tags # push all tags

# delete tags
git tag -d <tag name>
```

When there are tags, you can use `git describe` for a specific commit and it will output `<tag>_<numCommits>_g<hash>`, where `tag` is the closest ancestor tag in history, `numCommits` is how many commits away that tag is, and `hash` is the hash of the commit being described

# Git Branching

## Create and Delete Branches

```bash
# view branches
git branch

# switch to a branch
git switch <branch>
git switch -c <branch> # create and then switch

# delete a branch
git branch -d <branch> # delete a fully merged branch
git branch -D <branch> # force delete
```

## Combine works from two branches

### Merge

```bash
# merge a branch to current branch
git merge <branch>
```

```text
git merge topic master
      A---B---C topic            A---B---C topic
     /                 to       /         \
D---E---F---G master       D---E---F---G---H master
```

### Rebase

Usually rebase the main branch into the dev branch

```bash
# rebase a branch to current branch
git rebase <branch>

# Interactive rebase, reorder/drop commits
git rebase -i <commit>
git rebase -i HEAD~4
```

```text
git rebase master topic
      A---B---C topic                    A'---B'---C' topic
     /                 to               /
D---E---F---G master       D---E---F---G master
```

## Move Between Commits

```bash
# Point branch to a specific commit
git branch -f <branch> <commit>

# Point HEAD to commit
git checkout <commit>
```

## Cherry-pick

```bash
# copy a series of commits to current HEAD
git cherry-pick <commit1> <commit2> ...
```

## Solve Conflicts

When Git can't auto merge two branches, we need to merge the file with conflicts manually.

```bash
# config the merging tool
git config --global merge.tool meld

# merge with tool
git mergetool
```

# Undo Things

## Amend Previous Commit

```bash
git commit --amend
git commit --amend --no-edit # amend without changing the commit message
```

## Undo File

```bash
# unchange file
git restore <file>

# unstage
git restore --staged <file>
```

## Undo Commits

`HEAD` is a pointer that points to the current version.

```bash
# version rollback
git reset --hard

# rollback but keep the modification in staging area
git reset --soft

# rollback but keep the modification
git reset --mixed # default behavior for reset

# find the logs of history
git log

# find the logs for all git commands
git reflog
```

# Working With Remotes

==Remote branches are in local==

## GitHub Setup

Connecting over HTTPS doesn't require SSH Keys but you have to type your password every time.

Connecting over SSH requires generating SSH Keys on each computer.

```bash
# create SSH Key for authentication
ssh-keygen -t ed25519 -C "your_email@example.com"
```

Then copy the content in `id_ed25519.pub` to GitHub -> Account settings -> Add SSH Key.

```bash
# connect a local repo to github repo
git remote add origin git@github.com:example.git

# first time push local repo, need to connect local branch with server branch
git push --set-upstream origin master

# clone a repo from github
git clone git@github.com:example.git
```

## Show Remotes

```bash
git remote

# show the URLs for reading and writing
git remote -v

# show more information about a particular remote
git remote show <remote>
```

## Add Remote Repo

```bash
# give a name other that 'origin'
git remote add <shortname> <url>

# update remote branches
git fetch <remote>

# fetch & merge
git pull

# push your branch to your remote server
git push <remote> <branch>
```

## Rename and Remove Remotes

```bash
git remote rename a b
git remote remove a
```

# Setup

## Identity Setup

```bash
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
```

## Ignore Files

There might be some files that you don't want Git to track, for example, the log file and the file produced by building system. Then you can add a `.gitignore` file in your folder:

```bash
# ignore all .a files
*.a

# but do track lib.a, even though you're ignoring .a files above
!lib.a

# only ignore the TODO file in the current directory, not subdir/TODO
/TODO

# ignore all files in any directory named build
build/

# ignore doc/notes.txt, but not doc/server/arch.txt
doc/*.txt

# ignore all .pdf files in the doc/ directory and any of its subdirectories
doc/**/*.pdf
```

## Alias

`oh_my_zsh` has lots of alias for git, here list some commonly used ones:

```bash
# add
ga = 'git add'
gaa = 'git add --all' # stage all changes and new files

# commit
gc = 'git commit -v'
gc! = 'git commit -v --amend'
gcmsg = 'git commit -m'
gcn! = 'git commit -v --no-edit --amend' # amend without changing commit message
gca = 'git commit -v -a' # automatically stage modified and delted files, but not new files
gca! = 'git commit -v -a --amend'
gcam = 'git commit -a -m'
gcan! = 'git commit -v -a --no-edit --amend'

# status
gst = 'git status'

# log
glg = 'git log --stat'
glgp = 'git log --stat -p'
glgg = 'git log --graph'
glo = 'git log --oneline --decorate'

# branch
gb = 'git branch'
gbd = 'git branch -d'
gbD = 'git branch -D'

# switch
gsw = 'git switch'
gswc = 'git switch -c'

# checkout
gco = 'git checkout'

# push
gp = 'git push'
gpf = 'git push --force-with-lease' # rejects if there are new commits when we were rebasing
gpf! = 'git push --force'
gpsup = 'git push --set-upstream origin $(git_current_branch)' # first time push local branch

# fetch
gf = 'git fetch'

# pull
gl = 'git pull'
gup = 'git pull --rebase'

# cherry-pick
gcp = 'git cherry-pick'

# diff
gd = 'git diff'
gds = 'git diff --staged'

# stach
gsta = 'git stash push'
gstl = 'git stash list'
gstp 'git stash pop'
```

You can also add alias in `.gitconfig` file.

```bash
# use git unstage
git config --global alias.unstage "reset HEAD --"

# use git last
git config --global alias.last "log -1 HEAD"
```

# Reference

1. [Learn Git Branching](https://learngitbranching.js.org/): Online git learning tool with visualization
2. [Pro Git](https://git-scm.com/book/en/v2): Git book
