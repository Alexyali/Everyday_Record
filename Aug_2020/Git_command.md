# Git command

- how to work for a project in Git

## upload process

- `git pull` to update the repository

- `git add <changed_file>` , then it will store in local
- `git commit -s` to edit the message of this change, `ctrl + X` to exit. If commit message is wrong, use `git commit --amend` to fix it
- `git push <remote_name>` to push the commit in local to remote repository

## some useful command

- `git stash` to store the changed file shortly, use `git stash pop` to restore them
- `git rebase -i <commit id>` when a historical commit is wrong, and use `git rebase continue` to finish it. Maybe should use `git stash` if need store changed files
- `git log` to check history commit message
- `git status` to check local file change with remote branch
- `git diff` to see detail of change in each edited file

