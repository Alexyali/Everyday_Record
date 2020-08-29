# Everyday_Record
- github上传方法
```
git add .
git commit -m "notes"
git push -u origin master
```
- github拉取远程仓库方法
```
git pull origin master
```
- 将远程指定分支拉取到本地当前分支
```
git pull origin <branch name>
```

## ssh and scp
- use `ssh` to connect to server
```
$ ssh <usr_name>@<ip_addr>
```
- use `scp` to send file to server, open terminal in local machine
```
$ scp <local_file_addr> <usr_name>@<ip_addr>:<remote_addr>
```

- use `scp` to download file from server to local

```
scp username@servername:/path/filename /tmp/local_destination
```

- enc addr

```
172.16.7.84
```

