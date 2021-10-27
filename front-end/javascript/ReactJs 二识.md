## ReactJs 二识

### redux

**Redux 是一个使用叫做“action”的事件来管理和更新应用状态的模式和工具库** 它以集中式Store（centralized store）的方式对整个应用中使用的状态进行集中管理，其规则确保状态只能以可预测的方式更新。

**设计思想（如果你不知道是否需要 Redux，那就是不需要它）：**

- Web 应用是一个状态机，视图与状态是一一对应的
- 所有的状态，保存在一个对象里面

### setState

**setState 更新状态的2种写法**

(1). setState(stateChange, [callback])------对象式的setState

> 1.stateChange为状态改变对象(该对象可以体现出状态的更改)
> 2.callback是可选的回调函数, 它在状态更新完毕、界面也更新后(render调用后)才被调用

(2).setState(updater, [callback])------函数式的setState

> 1.updater为返回stateChange对象的函数。
> 2.updater可以接收到state和props。
> 3.callback是可选的回调函数, 它在状态更新、界面也更新后(render调用后)才被调用。

总结：

1.对象式的setState是函数式的setState的简写方式(语法糖)
2.使用原则：
				(1).如果新状态不依赖于原状态 ===> 使用对象方式
				(2).如果新状态依赖于原状态 ===> 使用函数方式
				(3).如果需要在setState()执行后获取最新的状态数据, 要在第二个callback函数中读取

