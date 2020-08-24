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

  