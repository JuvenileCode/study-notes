## Git 命令暇记

### 设置代理

```shell
# 设置代理(根据代理软件代理端口设置)
## http代理
git config --global https.proxy http://127.0.0.1:1089
git config --global https.proxy https://127.0.0.1:1089
## socks5代理
git config --global http.proxy 'socks5://127.0.0.1:1080'
git config --global https.proxy 'socks5://127.0.0.1:1080'
# 查看
git config --global  --get http.proxy
git config --global  --get https.proxy
# 取消代理
git config --global --unset http.proxy
git config --global --unset https.proxy
```

### 查看用户和邮箱

```shell
# 查看当前project
git config user.name   
git config user.email
# 修改当前project
git config user.name "update user"
git config user.email "update email"
# 修改全局name和email
git config --global user.name "update user"
git config --global user.email "update email"
```

### 远程仓库建立链接

```shell
# 命令
git remote add origin "链接地址"
# 例：
git remote add origin  http://39.101.200.176:8090/businessPlatform/uqian-data-model.git
```

### 远程仓库push和pull

```shell
# 拉取
git pull --rebase origin master
# 提交
1. git add || git add . 
2. git commit -m "commit info"
3. git push -u origin master
```



## 问题

### Mac Iterm2 Git Status 中文乱码

**core.quotepath没有设置好** : `git config --global core.quotepath false`

