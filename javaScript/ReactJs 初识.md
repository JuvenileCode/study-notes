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

#### 受控组件



#### 非受控组件

