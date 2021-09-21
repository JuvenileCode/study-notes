

## ReactJs 初识

### JSX语法规则

```typescript
<div id="text"></div>

<script src="https://unpkg.com/react@17/umd/react.production.min.js" crossorigin></script>
<script src="https://unpkg.com/react-dom@17/umd/react-dom.production.min.js" crossorigin></script>
<script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>
/* prop属性校验 */
<script src="https://unpkg.com/prop-types@15.6/prop-types.js"></script>
<script type="text/babel" >
  const data=['JAVA','ReactJs',"GoLang"]
 /*创建虚拟DOM*/
  const myId='userId';
  const VDOM = (
  <div>
    <h2 className="title" id={myId.toLowerCase()}>
    	<span style={{colro:'red'}}> {myId.toLowerCase()} </span>
  	</h2>
  	<ul>
  		{
  			data.map((item,index)=>{
  				return <li key={index}>{item}</li>
  			})
  		}
  	</ul>
  </div>
  )
  /*渲染虚拟DOM*/
  ReactDOM.render(VDOM,document.getElementById('test'))
</script>
```

1. 定义虚拟DOM时，不要写引号
2. 标签中混入JS表达式要用{}
3. 样式的类名不要指定用class，要用className
4. 内联样式，要用style={{ke:value}}的形式去写
5. 标签必须闭合
6. 标签首字母
   1. 若小写字母开头，则将标签转为对应html同名元素
   2. 若大些字母开头，react则去渲染对应的组件

### 模块与组件

**模块：**

随着业务逻辑增加，代码越来越多且复杂，拆成向外提供特定功能的js程序

**组件：**

用来实现局部功能的代码和资源的集合(html/css/js/...)等，直接复用全套

### React组件

#### 函数式组件

```typescript
<script type="text/babel">
  //1.创建函数式组件
  function Demo() {
  return (
    <div>
    <h2>函数式组件（适用于简单定义）</h2>
    <button type="button">按钮点击</button>
    </div>
  );
}

//2.渲染组件到页面
ReactDOM.render(<Demo/>, document.getElementById("test"));
</script>
```

#### 类式组件

**简单回顾JavaScript类的定义**

```javascript
<script type="text/javascript">
  // 定义一个person Class
  class Person {
    // 构造器方法
    constructor(name, age) {
      this.name = name;
      this.age = age;
    }
    // 一般方法（类的原型对象上，供实例使用）
    speak() {
      console.log(`I am${this.name},age is${this.age}`);
    }
  }

class Student extends Person {
  constructor(name, age, grade) {
    super(name, age);
    this.grade = grade;
  }

  speak() {
    console.log(`I am${this.name},age is${this.age},N J is ${this.grade}`);
  }
}
// 实例化person
let person = new Person('tom', 18);
console.log(person);
let student = new Student('ZhangSan', 24, 'Init One');
console.log(student);
</script>
```

**类式组件基本写法**

```typescript
<script type="text/babel">
  //1.创建类式组件
  class ClassComponent extends React.Component {
    render() {
      return (
        <div>
        <h2>类式组件（适用于复杂定义）</h2>
        <button type="button">按钮点击</button>
        </div>
      );
    }
  }
//2.渲染组件到页面
ReactDOM.render(<ClassComponent/>, document.getElementById("test"));
</script>
```

**小结：**

`函数式组件（简单组件）`和` 类式组件（复杂组件）`的区别在于**状态（state）**，有状态的就是复杂组件

### 事件

**通过onXxx属性指定事件处理函数（注意大小写）**

1. React使用的是自定义合成事件，而不是DOM原生事件
2. React中的事件是通过事件委托方处理的（委托给组件最外层元素 ）

**通过event.target得到发生事件的DOM元素对象**

### 组件三大核心属性

#### state（状态）

- state是组件对象最重要的属性，值是对象（多个key-value组合）

- 组件被称为“状态机”，通过更新组件的state来更新对应的页面显示（重新渲染组件）

- 状态数据不能直接修改，需要用`setState`方法来修改

```typescript
<script type="text/babel">

    class SimpleComponent extends React.Component {
        // 初始化状态
        state = {isHost: true, name: 'LiSi'};
        // 自定义方法 -- 赋值语句+箭头函数
        click = () => {
            // 修改state需要调用setState方法，直接this.state.isHost确实会更新，但react不会变
            this.setState({isHost: !this.state.isHost})
        }

        render() {
            let name = this.state.name;
            return (
                <div>
                    <h1 onClick={this.click}> {name} ! 今天天气很 {this.state.isHost ? 'Hot' : 'Cold'}～ ～ </h1>
                    <button type="button" onClick={this.click}>按钮点击</button>
                </div>
            )
        }
    }

    ReactDOM.render(<SimpleComponent/>, document.getElementById("test2"));

</script>
```

#### props

`props`是一个从外部传进组件的参数，主要作为就是从父组件向子组件传递数据，它具有**可读性**和**不变性**，只能通过外部组件主动传入新的`props`来重新渲染子组件，否则子组件的`props`以及展现形式不会改变。

```typescript
<script type="text/babel">
    class Person extends React.Component {
        // 为组件props参数提供校验
        static propTypes = {
            name: PropTypes.string.isRequired,
            sex: PropTypes.string,
            age: PropTypes.number,
        }
        // 指定默认值
        static defaultProps = {
            sex: 'woman',
            age: 18
        }

        render() {
            // props 属性是只读的
            let {name, sex, age} = this.props;
            return (
                <ul>
                    <li>姓名：{name}</li>
                    <li>性别：{sex}</li>
                    <li>年龄：{age + 1}</li>
                </ul>
            )
        }
    }
    // ...{} rest运算符
    ReactDOM.render(<Person {...{name: 'LouNa', age: 27, sex: 'man'}}/>, document.getElementById('test'))
    ReactDOM.render(<Person {...{name: '张三'}}/>, document.getElementById('test2'))
</script>
```

#### refs

**Refs 是一个 获取 DOM节点或 React元素实例的工具**

- 字符串ref , ~~简单但不推荐，官方可能会去掉~~
- 函数式ref
- React.createRef() ，官方推荐，每个值都得新定义

```typescript
<script type="text/babel">
    class Demo extends React.Component {
        // React.createRef() 使用ref方式，官方推荐，但一个myRef只能绑定一个元素
        myRef = React.createRef();
        click = () => {
            alert(this.input1.value + this.myRef.current.value)
        }

        render() {
            return (
                <div>
                    {/*<input type="text" ref="input1" placeholder="字符串的ref不推荐使用"/>
                     <input type="text" ref={c => this.input1 = c} placeholder="内联回调函数在更新会执行两次（一次清空，一次渲染）"/>
                  */}
                    <input type="text" ref={c => this.input1 = c} placeholder="点击按钮提示数据"/>
                    <button onClick={this.click}>onClick</button>
                    <input type="text" ref={this.myRef} onBlur={this.click} placeholder="失去焦点提示数据"/>
                </div>
            );
        }
    }

    ReactDOM.render(<Demo/>, document.getElementById('test'))
</script>
```



### 受控&非受控组件

#### 非受控组件

如果一个表单组件没有 value props(单选按钮和复选框对应的是 checked prop) 时，就可以称为非受控组件；比如用`ref`。

```typescript
<script type="text/babel">
    class Login extends React.Component {

        submitCall = (event) => {
            /*阻止表单提交*/
            event.preventDefault();
            alert(this.username.value)
        }

        render() {
            return (
                <form action="https://www.baidu.com" onSubmit={this.submitCall}>
                    USERNAME: <input ref={c => this.username = c} type="text" name="username"/>
                    PASSWORD: <input type="password" name="password"/>
                    <button>Login</button>
                </form>
            );
        }
    }

    ReactDOM.render(<Login/>, document.getElementById('test'))
</script>
```

#### 受控组件

在 React 中，表单元素通过组件的 state 属性来自己维护 state（vue中叫双向绑定，react没有双向绑定），并根据用户输入调用[setState()](https://link.segmentfault.com/?url=https%3A%2F%2Fzh-hans.reactjs.org%2Fdocs%2Freact-component.html%23setstate)来进行数据更新，使 React 的 state 成为“唯一数据源”，被 React 以这种方式控制取值的表单输入元素就叫做“受控组件”

```typescript
<script type="text/babel">
    class Login extends React.Component {
        // 初始化状态
        state = {
            username:'',
            password:''
        }

        nameChange = (event) => {
            console.log(event.target.value);
            this.setState({username:event.target.value});
        }

        submitCall = (event) => {
            /*阻止表单提交*/
            event.preventDefault();
            console.log(this.state.username);
        }

        render() {
            return (
                <form action="https://www.baidu.com" onSubmit={this.submitCall}>
                    USERNAME: <input onChange={this.nameChange} type="text" name="username"/>
                    PASSWORD: <input type="password" name="password"/>
                    <button>Login</button>
                </form>
            );
        }
    }

    ReactDOM.render(<Login/>, document.getElementById('test'))
</script>
```

### 高阶函数-柯里化

**高阶函数（满足任意一个规范）：**

- 若A函数，接收的参数是一个函数，那么A就可以称为高阶函数
- 若A函数，调用的返回值依然是一个函数，那么A也可以称为高阶函数

**函数的柯里化：**通过函数调用继续返回函数的方式，实现多次接收参数最后统一处理函数的编码形式

```javascript
<script type="text/babel">
    class Login extends React.Component {
        // 初始化状态
        state = {
            username: '',
            password: ''
        }

        // 函数柯里化写法
        saveFormData = (dataName) => {
            return (event) => {
                this.setState({[dataName]: event.target.value});
            }
        }

        // 不用函数柯里化实现
        ordinary = (key, value) => {
            this.setState({[key]: value});
        }

        submitCall = (event) => {
            /*阻止表单提交*/
            event.preventDefault();
            console.log(this.state.username);
        }

        render() {
            return (
                <form action="https://www.baidu.com" onSubmit={this.submitCall}>
                    USERNAME: <input onChange={this.saveFormData('username')} type="text" name="username"/>
                    PASSWORD: <input onChange={(event) => {this.ordinary('password',event.target.value)}} type="password" name="password"/>
                    <button>Login</button>
                </form>
            );
        }
    }

    ReactDOM.render(<Login/>, document.getElementById('test'))
</script>
```

### React生命周期

#### 旧版本

![react-old-lief](https://raw.githubusercontent.com/JuvenileCode/study-notes/master/image-source/old_react_life.png)

1. **初始化阶段：由ReactDOM.render()触发 --- 初次渲染**
   1. constructor()
   2. componentWillMount()
   3. render()
   4. componentDidMount()
2. **更新阶段：由组件内部this.setState()或父组件重新render触发**
   1. shouldComponentUpdate()
   2. componentWillUpdate()
   3. render()
   4. componentDidUpdate()
3. **卸载组件：由ReactDom.unmountComponentAtNode()触发**
   1. componentWillUnmount()

```javascript
<script type="text/babel">
  class Life extends React.Component {

    constructor(props) {
      console.log('[Current sum] constructor 构造器调用');
      super(props);
      this.state = {count: 0}
    }

    sum = () => {
      let {count} = this.state;
      count += 1;
      this.setState({count})
    }

    deleteComponent = () => {
      // 卸载组件
      ReactDOM.unmountComponentAtNode(document.getElementById('life'))
    }

    forciblyUpdate = () => {
      // 强制更新
      this.forceUpdate();
    }

    componentWillMount() {
      console.log('[Current sum] componentWillMount 组件将要挂载调用');
    }

componentDidMount() {
  console.log('[Current sum] componentDidMount 组件完成挂载调用');
}

componentWillUnmount() {
  console.log('[Current sum] componentWillUnmount 卸载组件调用');
}

shouldComponentUpdate() {
  console.log('[Current sum] shouldComponentUpdate 应用组件更新策略');
  // true 才会继续向下走
  return true;
}

componentWillUpdate() {
  console.log('[Current sum] componentWillUpdate 组件将要更新');
}

componentDidUpdate() {
  console.log('[Current sum] componentDidUpdate 组件更新完成');
}

render() {
  console.log('[Current sum] render 组件挂载调用');
  let {count} = this.state;
  return (
    <div>
    <h2> Current sum : {count}</h2>
<button onClick={this.sum}> onClick +1</button>
<button onClick={this.deleteComponent}> delete Component</button>
<button onClick={this.forciblyUpdate}>forcibly Update</button>
</div>
)
}
}

class One extends React.Component{
  state = {other:'no'}

onClick = ()=>{
  this.setState({other: this.state.other += '1'})
}

render(){
  return (
    <div>
    <h4> This is ONE Component</h4>
    <button onClick={this.onClick}> onClick</button>
<Two other={this.state.other}/>
  </div>
)
}
}

class Two extends React.Component{
  componentWillReceiveProps(props){
    console.log('[Two] componentWillReceiveProps 接收父组件props调用(初始化调用)',props);
  }
  render(){
    return (
      <div>
      <h4> This is TWO Component</h4>
      <h5> from ONE Component value: {this.props.other}</h5>
</div>
)
}
}

// ReactDOM.render(<Life/>, document.getElementById('life'))
ReactDOM.render(<One/>, document.getElementById('life'))
  </script>
```

#### 新版本

![new_react_life](https://raw.githubusercontent.com/JuvenileCode/study-notes/master/image-source/new_react_life.png)

1. **初始化阶段：由ReactDOM.render()触发 --- 初次渲染**
   1. constructor()
   2. getDerivedStateFromProps()
   3. render()
   4. componentDidMount()
2. **更新阶段：由组件内部this.setState()或父组件重新render触发**
   1. getDerivedStateFromProps()
   2. shouldComponentUpdate()
   3. render()
   4. getSnapshotBeforeUpdate()
   5. componentDidUpdate()
3. **卸载组件：由ReactDom.unmountComponentAtNode()触发**
   1. componentWillUnmount()

#### 新旧生命周期函数对比

**重要函数**

- render：初始化渲染或更新渲染调用
- componentDidMount：开启监听，发送ajax请求
- componentWillUnmount：收尾，清理逻辑

**即将废弃函数**

- componentWillMount
- componentWillReceiveProps
- componentWillUpdate

现在使用会出现警告，使用需要加上`UNSAFE_`  前缀

### Diffing算法

1. 虚拟DOM中key的作用:
   1). 简单的说:key是虚拟DOM对象的标识,在更新显示时key起着极其重要的作用。
   2). 详细的说:当状态中的数据发生变化时,react会根据【新数据】生成【新的虚拟DOM】
   随后React进行【新虚拟DOM】与【旧虚拟DOM】的diff比较,比较规则如下:
2. 旧虚拟DOM中找到了与新虚拟DOM相同的key:
    1). 若虚拟DOM中内容没变,直接使用之前的真实DOM
    2). 若虚拟DOM中内容变了,则生成新的真实DOM,随后替换掉页面中之前的真实DOM
    旧虚拟DOM中未找到与新虚拟DOM相同的key
   根据数据创建新的真实DOM,随后渲染到到页面
3. 用index作为key可能会引发的问题:
   1.若对数据进行:逆序添加、逆序删除等破坏顺序操作:
   会产生没有必要的真实DOM更新==>界面效果没问题,但效率低。
   2.如果结构中还包含输入类的DOM:
   会产生错误DOM更新==>界面有问题。
   3.注意!如果不存在对数据的逆序添加、逆序删除等破坏顺序操作,
   仅用于渲染列表用于展示,使用index作为key是没有问题的。
   3.开发中如何选择key?:
   1.最好使用每条数据的唯一标识作为key,比如id、手机号、身份证号、学号等唯一值。
   2.如果确定只是简单的展示数据,用index也是可以的。

```typescript
<script type="text/babel">
    class Person extends React.Component {
        state = {
            person: [
                {id: 1, name: 'Duo', age: 19},
                {id: 2, name: 'Anna', age: 20}
            ]
        }
        newInsert = () => {
            let {person} = this.state;
            // person.push({id: 3, name: 'Li', age: 18});
            this.setState({person: [{id: person.length + 1, name: 'Li', age: 18}, ...person]});
        }
        render() {
            return (
                <div>
                    <h2>show person info</h2>
                    <button onClick={this.newInsert}>new Insert</button>
                    <h3>use id key</h3>
                    <ul>
                        {this.state.person.map((value, index) => {
                            return <li key={value.id}>{value.name} === {value.age} <input type='text'/></li>
                        })}
                    </ul>
                    <h3>use index key</h3>
                    <ul>
                        {this.state.person.map((value, index) => {
                            return <li key={index}>{value.name} === {value.age} <input type='text'/></li>
                        })}
                    </ul>
                </div>
            );
        }
    }
    ReactDOM.render(<Person/>, document.getElementById('test'))
</script>
```

### 脚手架

1. **全局安装：** `npm i create-react-app -g`
2. **却换到创建项目目录：**`create-react-app test-react-pro`
3. **进入项目文件夹启动项目：**`npm start`

[目录结构说明](http://www.html.cn/create-react-app/docs/folder-structure/)



### 组件通信(数据传递)

#### 父=>子组件

父组件通过props把数据传给子组件，子组件通过`this.props`

```jsx
// 父组件直接传递
<List todos={todos}/>
// 子组件通过props接收
const {todos} = this.props
```

context对于解决react组件层级很深的props传递很有效，但也不应该被滥用

#### 子=>父组件

父组件定义回调函数props把数据传给子组件

```jsx
// 父组件 定义回调函数
deleteTodo = (id) => {
  const {todos} = this.state
  const newTodos = todos.filter(todo => {
    return todo.id !== id
  })
  this.setState({todos: newTodos});
}
// 通过props传递给子组件
<List deleteTodo={this.deleteTodo}/>
// 子组件使用
this.props.deleteTodo(id)
```

#### PubsubJs 消息队列通信

安装`npm i pubsub-js`

```jsx
// 导入包
import MessageQueue from 'pubsub-js';
//订阅共同的消息名

// 组件挂载页面后订阅消息
componentDidMount() {
  this.token = MessageQueue.subscribe('searchUser', (_, data) => {
    this.setState({...data})
  });
}
// 卸载组件取消消息订阅
componentWillUnmount() {
  MessageQueue.unsubscribe(this.token)
}

//发布消息
MessageQueue.publish('searchUser',{isFirst: false, isLoading: true});
```

### 路由

安装`npm i react-router-dom`

```tsx
<div className="list-group">
  {/*在React中靠路由链接实现组件却换--编写路由链接*/}
  <NavLink activeClassName="active" className="list-group-item " to="/home">Home</NavLink>
  <NavLink activeClassName="active" className="list-group-item " to="/about">About</NavLink>
</div>

<div className="panel-body">
  {/*注册路由*/}
  <Route path="/home" component={Home}/>
  <Route path="/about" component={About}/>
</div>
```

#### 基本使用

		1.明确好界面中的导航区、展示区
		2.导航区的a标签改为Link标签
					<Link to="/xxxxx">Demo</Link>
		3.展示区写Route标签进行路径的匹配
					<Route path='/xxxx' component={Demo}/>
		4.<App>的最外侧包裹了一个<BrowserRouter>或<HashRouter>

#### 路由组件与一般组件

```
		1.写法不同：
					一般组件：<Demo/>
					路由组件：<Route path="/demo" component={Demo}/>
		2.存放位置不同：
					一般组件：components
					路由组件：pages
		3.接收到的props不同：
					一般组件：写组件标签时传递了什么，就能收到什么
					路由组件：接收到三个固定的属性
										history:
													go: ƒ go(n)
													goBack: ƒ goBack()
													goForward: ƒ goForward()
													push: ƒ push(path, state)
													replace: ƒ replace(path, state)
										location:
													pathname: "/about"
													search: ""
													state: undefined
										match:
													params: {}
													path: "/about"
													url: "/about"
```

**NavLink可以实现路由链接的高亮，通过activeClassName指定样式名**

#### Switch的使用

1.通常情况下，path和component是一一对应的关系。
2.Switch可以提高路由匹配效率(单一匹配)。

**多级路径刷新页面样式丢失的问题:**

1.public/index.html 中 引入样式时不写 `./ 写 / `（常用）
2.public/index.html 中 引入样式时不写` ./ 写 %PUBLIC_URL% `（常用）
3.使用HashRouter

#### 路由的严格匹配与模糊匹配

1.默认使用的是模糊匹配（简单记：【输入的路径】必须包含要【匹配的路径】，且顺序要一致）
2.开启严格匹配：`<Route exact={true} path="/about" component={About}/>`
3.严格匹配不要随便开启，需要再开，有些时候开启会导致无法继续匹配二级路由

#### 嵌套路由

1.注册子路由时要写上父路由的path值
2.路由的匹配是按照注册路由的顺序进行的

```jsx
<MyNavLink to="/home/news">News</MyNavLink>
<Route path="/home/news" component={News}/>
```

#### 向路由组件传递参数

**params result风格参数：**

> a. 路由链接(携带参数)：`<Link to='/demo/test/tom/18'}>详情</Link>`
> b. 注册路由(声明接收)：`<Route path="/demo/test/:name/:age" component={Test}/>`
> c. 接收参数：`this.props.match.params`

**search参数:**

> 注册路由(无需声明，正常注册即可)：`<Route path="/demo/test" component={Test}/>`
> 接收参数：this.props.location.search
> **备注**：获取到的search是urlencoded编码字符串，需要借助`querystring`解析

**state参数:**

> 路由链接(携带参数)：`<Link to={{pathname:'/demo/test',state:{name:'tom',age:18}}}>详情</Link>`
> 注册路由(无需声明，正常注册即可)：`<Route path="/demo/test" component={Test}/>`
> 接收参数：this.props.location.state
> 备注：刷新也可以保留住参数

#### 编程式路由导航

```jsx
//借助this.prosp.history对象上的API对操作路由跳转、前进、后退
  -this.prosp.history.push()
  -this.prosp.history.replace()
  -this.prosp.history.goBack()
  -this.prosp.history.goForward()
  -this.prosp.history.go()
```

