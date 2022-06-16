## Linux 命令暇记

### 命令帮助

**内建命令与外部命令：**

Linux 的**内建命令**是 shell 程序的一部分，Linux 系统加载运行时就被加载并驻留在系统内存里的，因此执行速度较快。

Linux 的**外部命令**是通过额外安装获得的命令，不随系统一起被加载到内容中，运行速度慢但功能强大。

```powershell
# type 命令可以查看该命令是内建命令还是外部命令 type <command>
type mkdir
```

#### help 与 --help 

```powershell
# help <command> 只能用来查看内建命令帮助
help ls
# <command> --help  查看外部命令帮助
mv --help
```

#### man

man 是 manual 的简写(`man [option] <command>`)，与 help 命令和 --help 参数不同，使用 man 命令查询帮助手册时会进入 man page 界面，而非直接打印在控制台上。

**man page 操作：**

> 空格键  翻页
>
> /str 向后查找    ?str 向前查找
>
> n 搜索到的下一个字符串   N 搜索到的上一个字符串
>
> q 退出

### 端口开放

#### 查看端口号是否开放

```bash
# 查询已开放的端口号 netstat  -ntulp | grep
netstat  -ntulp | grep 7473 
# 查询指定端口号是否开启
firewall-cmd --query-port=7473/tcp
```

#### 开放端口号

```bash
# 添加指定需要开放的端口
firewall-cmd --add-port=7473/tcp --permanent
# 重载入添加的端口
firewall-cmd --reload
# 移除指定端口
firewall-cmd --permanent --remove-port=7473/tcp
```

### 基础命令

[参考](https://www.cnblogs.com/peida/tag/%E6%AF%8F%E6%97%A5%E4%B8%80linux%E5%91%BD%E4%BB%A4/default.html?page=3)

#### cd(changeDirectory) 

```shell
cd /user/root/
# 进入跟目录
cd /
# 进入用户目录
cd ~ 
# 进入上一次工作目录
cd -
```

#### pwd当前路径

```powershell
# 查看当前路径
pwd
# 查看软链接的实际路径
pwd -P
```

#### mkdir创建文件夹

```powershell
# 前工作目录下创建名为 test 的文件夹
mkdir test
# 创建test3目录 并赋予权限
mkdir -m 777 test3
# 在 tmp 目录下创建路径为 test/t1/t 的目录，若不存在，则创建：
mkdir -p /tmp/test/t1/t
```

#### ls查看

```shell
# 查看所有及隐藏文件
ls -al
# 查看文件大小
ls -lh
# 查找当前目录中的所有jar文件
ls -l | grep '.jar' 
```

#### rm删除

```powershell
# 删除任何 .log 文件，删除前逐一询问确认
rm -i *.log
# 删除 test 子目录及子目录中所有档案删除，并且不用一一确认
rm -rf test
# 删除以 -f 开头的文件
rm -- -f*
```

#### mv移动或重命名

```powershell
# 将文件test.log重命名为test1.txt
mv test1.txt test2.txt
# 将test1.txt文件移到目录test3中
mv test1.txt test3
# -f ：force 强制的意思，如果目标文件已经存在，不会询问而直接覆盖
# -i ：若目标文件 (destination) 已经存在时，就会询问是否覆盖
# -u ：若目标文件已经存在，且 source 比较新，才会更新(update)
```

#### cp复制

```shell
# log.log复制到bark目录,目标文件存在时，会询问是否覆盖
cp log.log bark
# 复制整个目录
cp -a contents-a contents-b
```

#### touch新建文件

```shell
# 创建不存在的文件
touch a.log
```



### 查看文件

#### cat & nl打印

cat命令的用途是连接文件或标准输入并打印。这个命令常用来显示文件内容，或者将几个文件连接起来显示，或者从标准输入读取内容并显示，它常与重定向符号配合使用

```shell
# 查看文件
cat filename.txt
# 把 log2012.log 的文件内容加上行号后输入 log2013.log 这个文件里
cat -n log2012.log log2013.log

# 用 nl 列出 log2012.log 的内容(默认空白行,不会加上行号)
nl log2012.log 
```

#### less 文件查看

less是linux正统查看文件内容，能前后翻页，搜索等

```shell
# 查看文件,带行号
less -N log2013.log
# 从文件第4行开始查看
less +4 -N 01-meta视口标签.html
```

**操作：**

/字符串：向下搜索“字符串”的功能

?字符串：向上搜索“字符串”的功能

n：重复前一个搜索（与 / 或 ? 有关）

N：反向重复前一个搜索（与 / 或 ? 有关）

[pagedown]： 向下翻动一页

[pageup]：  向上翻动一页

#### head & tail

head从开头查看文件

```shell
# 显示文件的前5行
head -n 5 log2014.log
# 输出文件除了最后n行的全部内容
head -n -6 log2014.log
```

tail从文件尾部查看

```shell
# 实时显示日志最新内容
tail -f app.log
# 显示文件最后5行内容
tail -n 5 app.log 
```



### 搜索查找

#### which & whereis

which在PATH变量指定的路径中，搜索某个系统命令的位置

```shell
# 查找arthas是否安装
which arthas
```

**whereis**命令只能用于程序名的搜索，而且只搜索二进制文件（参数-b）、man说明文件（参数-m）和源代码文件（参数-s）

```shell
whereis svn
# svn: /usr/bin/svn /usr/local/svn /usr/share/man/man1/svn.1.gz
```

#### locate

locate命令用于查找文件，它需要一个数据库，数据库由updatedb程序来更新，updatedb是由cron daemon周期性建立

```shell
# 搜索etc目录下，所有以m开头的文件
locate /etc/m
```

#### *find

[更多参考指南](https://www.cnblogs.com/wanqieddy/archive/2011/06/09/2076785.html)

```shell
# 搜索文件夹(/ 代表根目录)
find / -name 'rocket*' -type d
# 搜索当前目录义april开头的文件(.代表当前目录)
find .  -name april*
# 查找/opt/soft/test/目录下 权限为 777的文件
find /opt/soft/test/ -perm 777
# 查找当前目录大于1K的文件
find . -size +1000c -print

```

**find 之 exec(-exec 参数后面跟的是command命令)**

 exec选项后面跟随着所要执行的命令或脚本，然后是一对儿{ }，一个空格和一个\，最后是一个分号（ ` -exec ls -l {} \;`）

```shell
# find命令匹配到了当前目录下的'*.log'，并在-exec选项中使用ls -l命令将它们列出
find . -name '*.log' -type f -exec ls -l {} \;
# -exec中使用grep命令；find命令首先匹配所有文件名为“ passwd*”的文件,然后执行grep命令看看在这些文件中是否存在一个root用户
find /etc -name "passwd*" -exec grep "root" {} \;
# 用exec选项执行cp命令  
find . -name "*.log" -exec cp {} test3 \;
# 目录中查找更改时间在n日以前的文件并删除它们，在删除之前先给出提示
find . -name "*.log" -mtime +5 -ok rm {} \;
# 查找避开多个目录
find test \( -path test/test4 -o -path test/test3 \) -prune -o -print
```

**find 之 xargs**

 find命令的-exec有处理长度限制，而sxargs命令则只有一个进程命令分批处理

```shell
# 查找系统中的每一个普通文件，然后使用xargs命令来测试它们分别属于哪类文件
find . -type f -print | xargs file
# 在当前目录下查找所有用户具有读、写和执行权限的文件，并收回相应的写权限
find . -perm -7 -print | xargs chmod o-w
# 用grep命令在当前目录下的所有普通文件中搜索hostnames这个词
find . -name \* -type f -print | xargs grep "hostnames"
```



### 输出当前用户环境变量

> `export` 或查询具体某个属性 `export $JAVA_HOME`
>
> 修改
>
> export JAVA_HOME = /url/

### 给文件赋权限

> // chown [用户] [文件路径] -R 
>
> chown esuser /home/smb/data/kibana -R

### 文件解压

> // *.tar.gz 解压
>
> tar -zxvf XX.tar.gz
>

### nohup.out文件清理 

> cp /dev/null nohup.out   **//:** 程序在运行清理后，只要有输入会还原，但程序停止清理有效
>
> nohup java -jar /xxx/xxx/xxx.jar >> start.log &  **//:** 用cp /dev/null >start.log 置空