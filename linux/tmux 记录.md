## tmux 记录

### tmus是什么？

[笔记参考来源](https://www.ruanyifeng.com/blog/2019/10/tmux.html)

**会话与进程**

命令行的典型使用方式是，打开一个终端窗口（terminal window，以下简称"窗口"），在里面输入命令。**用户与计算机的这种临时的交互，称为一次"会话"（session）**

>**问题：**[SSH 登录](https://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html)远程计算机，打开一个远程窗口执行命令。这时，网络突然断线，再次登录的时候，是找不回上一次执行的命令的。因为上一次 SSH 会话已经终止了，里面的进程也随之消失了

**Tmux 就是会话与窗口的"解绑"工具，将它们彻底分离。**

### 基本用法

#### 安装

```shell
# tmux 一般需要自己安装，检查是否已经安装
$ which tmux

# Ubuntu 或 Debian
$ sudo apt-get install tmux

# CentOS 或 Fedora
$ sudo yum install tmux

# Mac
$ brew install tmux
```

#### 启动与退出

Tmux 窗口有大量的快捷键。所有快捷键都要通过前缀键唤起。默认的前缀键是`ctrl+b`，即先按下`ctrl+b`，快捷键才会生效。

```bash
# 启动
$ tmux
# 自定义名字 (tmux new -s <session-name>)
$ tmux new -s new-test

# 退出
$ exit
# 或者按下 ctrl+d 
```

**最简单的操作流程**

>1. 新建会话`tmux new -s my_session`。
>2. 在 Tmux 窗口运行所需的程序。
>3. 按下快捷键`Ctrl+b d`将会话分离。
>4. 下次使用时，重新连接到会话`tmux attach-session -t my_session`。

#### 会话管理

```bash
# 查看当前所有的 Tmux 会话
$ tmux ls
# or
$ tmux list-session

# 重新接入某个已存在的会话
# 使用会话编号
$ tmux attach -t 0
# 使用会话名称
$ tmux attach -t <session-name>

# 杀死会话
# 使用会话编号
$ tmux kill-session -t 0
# 使用会话名称
$ tmux kill-session -t <session-name>

# 切换会话
# 使用会话编号
$ tmux switch -t 0
# 使用会话名称
$ tmux switch -t <session-name>
```

#### 会话快捷键

**在tmux窗口操作，按下前缀键`ctrl+b`**

>- `Ctrl+b d`：分离当前会话。
>- `Ctrl+b s`：列出所有会话。
>- `Ctrl+b $`：重命名当前会话。

#### 窗格管理

**Tmux 可以将窗口分成多个窗格（pane），每个窗格运行不同的命令**

```bash
# 划分窗格
# 划分上下两个窗格
$ tmux split-window
# 划分左右两个窗格
$ tmux split-window -h

# 移动光标
# 光标切换到上方窗格
$ tmux select-pane -U
# 光标切换到下方窗格
$ tmux select-pane -D
# 光标切换到左边窗格
$ tmux select-pane -L
# 光标切换到右边窗格
$ tmux select-pane -R

# 交换窗格位置
# 当前窗格上移
$ tmux swap-pane -U
# 当前窗格下移
$ tmux swap-pane -D
```

#### 窗格快捷键

>- `Ctrl+b %`：划分左右两个窗格。
>- `Ctrl+b "`：划分上下两个窗格。
>- `Ctrl+b <arrow key>`：光标切换到其他窗格。`<arrow key>`是指向要切换到的窗格的方向键，比如切换到下方窗格，就按方向键`↓`。
>- `Ctrl+b ;`：光标切换到上一个窗格。
>- `Ctrl+b o`：光标切换到下一个窗格。
>- `Ctrl+b {`：当前窗格与上一个窗格交换位置。
>- `Ctrl+b }`：当前窗格与下一个窗格交换位置。
>- `Ctrl+b Ctrl+o`：所有窗格向前移动一个位置，第一个窗格变成最后一个窗格。
>- `Ctrl+b Alt+o`：所有窗格向后移动一个位置，最后一个窗格变成第一个窗格。
>- `Ctrl+b x`：关闭当前窗格。
>- `Ctrl+b !`：将当前窗格拆分为一个独立窗口。
>- `Ctrl+b z`：当前窗格全屏显示，再使用一次会变回原来大小。
>- `Ctrl+b Ctrl+<arrow key>`：按箭头方向调整窗格大小。
>- `Ctrl+b q`：显示窗格编号。

#### 窗口管理

**除了将一个窗口划分成多个窗格，Tmux 也允许新建多个窗口**

```bash
# 建新窗口 tmux new-window
$ tmux new-window
# 新建一个指定名称的窗口
$ tmux new-window -n <window-name>

# 切换窗口
# 切换到指定编号的窗口
$ tmux select-window -t <window-number>
# 切换到指定名称的窗口
$ tmux select-window -t <window-name>
```

#### 窗口快捷键

>- `Ctrl+b c`：创建一个新窗口，状态栏会显示多个窗口的信息。
>- `Ctrl+b p`：切换到上一个窗口（按照状态栏上的顺序）。
>- `Ctrl+b n`：切换到下一个窗口。
>- `Ctrl+b <number>`：切换到指定编号的窗口，其中的`<number>`是状态栏上的窗口编号。
>- `Ctrl+b w`：从列表中选择窗口。
>- `Ctrl+b ,`：窗口重命名。

#### 其它命令

```bash
# 列出所有快捷键，及其对应的 Tmux 命令
$ tmux list-keys

# 列出所有 Tmux 命令及其参数
$ tmux list-commands

# 列出当前所有 Tmux 会话的信息
$ tmux info

# 重新加载当前的 Tmux 配置
$ tmux source-file ~/.tmux.conf
```

