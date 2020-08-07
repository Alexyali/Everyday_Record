# tmux 
`https://danielmiessler.com/study/tmux/`
- install
```
sudo apt install tmux
```
- 启动tmux，直接在终端输入`tmux`

## 窗格操作
```
# 划分上下两个窗格
$ tmux split-window 
# 划分左右两个窗格
$ tmux split-window -h
```
- 在不同窗格移动光标
```
$ tmux select-pane -L
$ tmux select-pane -R
```
- 快捷键
	- `ctrl+b %`：划分左右窗格
	- `ctrl+b "`：划分上下窗格
	- `ctrl+b ;`：切换到上一个窗格
	- `ctrl+b o`：切换到下一个窗格
	- `ctrl+b <arrow>`：按方向键，切换光标所在的窗格

## 窗口管理
- 新建窗口
```
tmux new-window -n <name>
```
- 切换窗口
```
tmux select-window -t <name>
```
- 快捷键
	- `ctrl+b c`：创建新窗口
	- `ctrl+b p`：切换上一窗口
	- `ctrl+b n`：切换下一窗口
	- `ctrl+b w`：从列表中选择窗口

## 滚动屏幕
- `ctrl+b PgUp/PgDn`：上下翻滚屏幕