## Arthas 诊断工具

[官网链接](https://arthas.aliyun.com/zh-cn/)

### 安装 & 启动 

```shell
# install
curl -O https://arthas.aliyun.com/math-game.jar
# start (会打印java进程,选择自己要观察的进程既可)
java -jar math-game.jar
```



### 命令说明

#### dashboard

> 当前系统的实时数据面板(当运行在Ali-tomcat时，会显示当前tomcat的实时信息，如HTTP请求的qps, rt, 错误数, 线程池信息等等)，按 ctrl+c 退出

#### thread

> 查看当前线程信息，查看线程的堆栈

```shell
# 查询线程id为2的信息
thread 2
# 展示当前最忙的前3个线程并打印堆栈
thread -n 3
# 找出当前阻塞其他线程的线程
thread -b
```

#### jad

> `jad` 命令将 JVM 中实际运行的 class 的 byte code 反编译成 java 代码

```shell
# 反编译代码 只显示源代码
jad --source-only com.uqian.framework.controller.DataInsertController
# 反编译指定函数
jad --source-only com.uqian.framework.controller.DataInsertController initOneAndTwoQuota

```

#### watch

> 方法执行数据观测，能观察到的范围为：`返回值`、`抛出异常`、`入参` ；能在 4 个不同的场景观察对象
>
> - watch 命令定义了4个观察事件点，即 `-b` 方法调用前，`-e` 方法异常后，`-s` 方法返回后，`-f` 方法结束后

```shell
# 观察表达式，默认值是{params, target, returnObj}
# [x] 指定输出结果的属性遍历深度，默认为 1
watch com.uqian.framework.controller.DataInsertController initOneAndTwoQuota -x 2

```

#### stack

> 输出当前方法被调用的调用路径

```shell
# primeFactors 调用路径
stack demo.MathGame primeFactors
```

#### trace

> `trace` 命令能主动搜索 `class-pattern`／`method-pattern` 对应的方法调用路径，渲染和统计整个调用链路上的所有性能开销和追踪调用链路

```shell
# 基本运行
trace demo.MathGame run
# 据调用耗时过滤 （只会展示耗时大于10ms的调用路径）
trace demo.MathGame run '#cost > 10'
```

