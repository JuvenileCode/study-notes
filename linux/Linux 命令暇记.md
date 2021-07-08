## Linux 命令暇记

### 查找文件夹

> `find / -name 'rocket*' -type d`

### 输出当前用户环境变量
> `export` 或查询具体某个属性 `export $JAVA_HOME`

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