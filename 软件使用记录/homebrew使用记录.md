## homebrew使用记录

### 设置代理

```shell
# ssr
export all_proxy=socks5://127.0.0.1:1086
# v2rayn
export all_proxy=socks5://127.0.0.1:1080
# 查看设置后代理IP地址
curl http://myip.ipip.net/
```

### brew 安装

> /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

### brew 卸载

>ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"

### brew常用命令

1. 安装卸载软件

   ```shell
   1. brew --version或者brew -v 显示brew版本信息
   2. brew install <formula> 安装指定软件
   3. brew unistall <formula 卸载指定软件
   4. brew list 显示所有的已安装的软件
   5. brew search text 搜索本地远程仓库的软件，已安装会显示绿色的勾
   6. brew search /text/ 使用正则表达式搜软件
   ```

   > 通过brew安装的文件会自动设置环境变量，所以不用担心命令行不能启动的问题。
   >  比如安装好了`gradle`，即可运行` gradle -v`

2. 升级软件

   ```shell
   1. brew update 自动升级homebrew（从github下载最新版本）
   2. brew outdated 检测已经过时的软件
   3. brew upgrade 升级所有已过时的软件，即列出的以过时软件
   4. brew upgrade <formula>升级指定的软件
   5. brew pin <formula> 禁止指定软件升级
   6. brew unpin <formula> 解锁禁止升级
   7. brew upgrade --all 升级所有的软件包，包括未清理干净的旧版本的包
   ```

3. 清理

   ```shell
   1. brew cleanup -n 列出需要清理的内容
   2. brew cleanup <formula> 清理指定的软件过时包
   3. brew cleanup 清理所有的过时软件
   4. brew unistall <formula> 卸载指定软件
   5. brew unistall <fromula> --force 彻底卸载指定软件，包括旧版本
   ```

   

