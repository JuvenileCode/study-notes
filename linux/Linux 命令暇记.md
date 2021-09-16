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



### 搜索查找

#### find

[更多参考指南](https://www.cnblogs.com/wanqieddy/archive/2011/06/09/2076785.html)

```shell
# 搜索文件夹
find / -name 'rocket*' -type d
# 搜索当前目录义april开头的文件
find   -name april*
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