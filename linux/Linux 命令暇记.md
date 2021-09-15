## Linux 命令暇记

### 查找

#### 查看

```shell
# 查看所有及隐藏文件
ls -al
# 查看文件大小
ls -lh
```

#### 搜索

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