## ReactJs 初识

### JSX语法规则

```html
<div id="text"></div>

<script src="https://unpkg.com/react@17/umd/react.production.min.js" crossorigin></script>
<script src="https://unpkg.com/react-dom@17/umd/react-dom.production.min.js" crossorigin></script>
<script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>
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

