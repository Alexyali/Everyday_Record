# Git command

- how to work for a project in Git

## upload process

- `git pull` to update the repository

- `git add <changed_file>` , then it will store in local
- `git commit -s` to edit the message of this change, `ctrl + X`  and `Enter` to exit. If commit message is wrong, use `git commit --amend` to fix it
- `git push <remote_name>` to push the commit in local to remote repository

## some useful command

- `git stash` to store the changed file shortly, use `git stash pop` to restore them
- `git rebase -i <commit id>` when a historical commit is wrong, and use `git rebase continue` to finish it. Maybe should use `git stash` if need store changed files
- `git log` to check history commit message
- `git status` to check local file change with remote branch
- `git diff` to see detail of change in each edited file
- `git branch -a` to see all branches
- `git checkout <branch_name>` to switch to one certain branch

## Problems

- detached HEAD 头指针分离

```
# 强制将 master 分支指向当前头指针的位置
$ git branch -f master HEAD
# 切换到 master 分支
$ git checkout master
```

- `Gitlab`本地+远程commit版本回退
```c++
# detect commit <id>
$ git log
# local commit reset
$ git reset --hard <id>
# remote commit reset
# first get into Gitlab website, enter `Settins`->`Repository`->`Protected
# Branches`->`expend` and click `unprotect`
$ git push <remote_name>/`orign master` --force
# get into Gitlab website and click `protect`
```

- `Gitlab`本地和fork的远程仓库同步，但是和远程主仓库不同步
```c++
# create two branch in local, one to follow remote orign, and 
# another to follow remote forked. 
# copy local master branch to a new branch, host_server is its name
$ git checkout -b host_server
# reset local master branch to last commit version if needed
# use `git log` to find commit id
$ git checkout master
$ git reset --hard <commit_id>
# synchronize local master with remote origin master
$ git pull origin master
# now you have two local branch, and master follows origin master
# if you want to let remote fork follow host_server, you can
$ git checkout host_server
$ git push myfork host_server:master
# now you want to edit host_server based on master
$ git checkout host_server
$ git rebase master
# use git status to find corruption files and fix them
$ git status
# after edit one file successfully, you need continue to fix next
$ git add <file_name>
$ git rebase --continue
# if you do not want to rebase, you can use abort to return 
$ git rebase --abort
```