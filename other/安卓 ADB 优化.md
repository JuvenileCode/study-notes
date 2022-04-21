## 安卓 ADB 优化

入手日本SONY手机，[遂优化](https://www.senayuki.moe/index.php/archives/477/)

### ADB卸载系统软件

[参考](https://www.cxybb.com/article/qq_30878303/109181675)

**1、想要卸载需要找对对应的软件包名**

- 手机安装黑域，可以查看对应的软件包名

**通过ADB查询**

```shell
# 进入adb shell
adb shell
# 列出所有包名 (太多, 有可能你以为的不是你以为的)
pm list packages

# 通过关键字查询
pm list packages | grep "docomo"

# 通过adb 查看当前界面的信息
dumpsys window | grep mCurrentFocus
# 输出例：mCurrentFocus=Window{33613e4 u0 com.baidu.haokan/com.baidu.haokan.app.activity.HomeActivity}
# 这里 window{} 中就是这个界面的包名类名，包名就是：com.baidu.haokan
```

**2、拿到包名后卸载**

```shell
# pm uninstall -k --user 0 包名
pm uninstall -k --user 0 com.baidu.haokan
# -k 表示保存数据，如不需要，可去掉 -k。
# --user 指定用户 id，Android 系统支持多个用户，默认用户只有一个，id=0
```

### ADB将SD卡转为内部存储

[参考](https://waylau.com/use-adb-shell-to-convert-sd-card-to-internal-storage/)

```shell
# 进入adb shell交互
adb shell

# 找到SD卡
sm list-disks 
# 例：假如返回的SD卡盘号是 179,64

# 设置为内部存储
sm partition disk:179,64 private

# 设置为便携式存储
sm partition disk:179,64 public

# 设置混合模式：以“sm partition disk:179,64 mixed 70”为例，意思是把sd卡容量的70%设置为便携式存储（存储照片等），30%设置为内部存储（存储软件）
sm partition disk:179,64 mixed 50 
```

**验证**

成功之后，打开 设置->存储设备

**移动应用到新的SD卡的内部存储**

手机设置-其它设置-开发者选项-强制允许将应用写入外部存储 打开