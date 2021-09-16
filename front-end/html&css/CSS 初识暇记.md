## CSS 初识暇记

[CSS参考手册在线查询](https://www.xp.cn/css3/)

### 初识

**CSS规则由选择器、一条多条声明构成**

```html
<style>
 /* 直接对html标签修改样式 */
  p {
    color: red;
    font-size: 18px;
  }
</style>
```

**CSS 代码风格**

```html
<!-- 1.样式格式 -->
<style> 
  /*1.1紧凑型*/ 
  h3{color: #2db7f5;font-size: 20px}
  /*1.2展开型*/
   p {
     color: red;
     font-size: 18px;
  }
</style>
<!-- 2.样式大小写：选择器、属性名、属性值推荐全部使用小写字母 -->
```

### CSS基础选择器

**选择器( [CSS选择器参考手册](https://www.w3school.com.cn/cssref/css_selectors.asp))就是可以任意选择HTML上的任意标签，用于定位HTML元素**

#### 基础选择器

**基础选择器由单个选择器组成：**

含：标签选择器、类选择器、id选择器和通配符选择器

```html
<!-- 1.标签选择器：写上标签名-->
<style>
  div {
    color： "green";
  }
</style>

<!-- 2.类选择器：单独选择标签-->
<style>
  /* 样式点定义，HTML(class=li-read)调用；最常用 */
  .li-red {
    color: #13c2c2;
  }
  .li-font {
    font-size: 22px;
  }
</style>
<ul>
    <li class="li-red">李香兰</li>
     <!-- 多个类选择器使用：多个类名空格隔开-->
    <li class="li-red li-font">飞鸟与蝉</li>
</ul>

<!-- 3.id选择器：前缀加#-->
<style>
  /* 结构id="id-read"调用，只能调用一次*/
  #id-read {
    color: greenyellow;
  }
</style>
<div id="id-read"></div>

<!-- 4.通配符选择器，修改所有元素-->
<style>
  * {
    page-policy: first;
  }
</style>
```

#### 复合选择器

**复合选择器由两个或者多个基础选择器通过不同的方式组合而成**

常用的用：后代选择器，子选择器，并集选择器，伪类选择器等

```html
<!-- 1.后代选择器: 多个元素空格隔开；元素可以是任意基础选择器-->
 <style>
   ol li{
     color: #18b566;
   }
   
   .cselect li{
     color: red;
   }
</style>
<ol>
    <li>one</li>
</ol>
<ul class="cselect">
    <li>ul-one</li>
</ul>

<!-- 2. 子选择器: 只选择某元素最近一级子元素；元素用（>）隔开； -->
<style>
  div>a {
    text-decoration: none;
    color: #1890ff;
  }
</style>
<div>
    <a href="#">链接1</a>
    <p>
        <a href="#">链接</a>
    </p>
</div>

<!-- 3.并集选择器：为多组标签定义相同的样式；元素用（,)隔开 -->
<style>
  #bj-div,#bj-p {
    color: #52c41a;
    text-shadow:1px 1px 1px rgba(0,0,0,.3);
  }
</style>
<div id="bj-div">AA</div>
<p id="bj-p">BB</p>
<span>CC</span>
<ul>
    <li>DD</li>
</ul>

<!-- 4.伪类选择器： 用于某些标签添加特殊效果；用冒号（：）隔开-->
<style>
/*链接伪类选择器*/
  a:link{选择所有未被访问的链接}
  a:visited{选择所有已被访问链接}
/*focus伪类选择器*/
  input:focus{background-color: yellow;}
</style>
```

**更多选择器可以[参考](https://www.runoob.com/cssref/css-selectors.html)**

### CSS字体属性

 ```html
 <style>
   /* font-family 定义文本字体 */
   p { font-family: "微软雅黑","Microsoft Yahei"}
   /* 设置字体大小、粗细和文字样式*/
   font-text {
     font-size: 16px;
     font-weight: bold;
     font-style: normal;
   }
   /* 复合属性 font: font-style font-weight font-size/line-height font-family*/
   complex {font: italic bold 16px Helvetica}
 </style>
 ```

### CSS文本属性

```html
<style>
  /* 文本颜色、对齐文本、装饰文本、文本缩进、行间距*/
  font-text {
    color: read;
    text-align: center;
    text-decoration: underline || none;
    /* 首行缩进 em相对当前文字距离位置*/
    text-indent: 20px || 4em;
    /* 上间距-文本高度-下间距组成：比如文字16px,那么上下间距为5px*/
    text-height: 26px;
    /* 文本居中 line-height == height 值文字就居中 */
    line-height: 30px;
  }
</style>
```

### CSS引入方式

```html
<!-- 1.行内样式表-->
<p style="color: read;font-size: 12px"></p>
<!-- 2.内部样式表-->
<style>  </style>
<!-- 3.外部样式表；单独写到CSS文件中，然后引入-->
<link ref="stylesheet" href="路径"/>
```

### Emmet语法

**[Emmet](https://code.z01.com/emmet/) (前身为 Zen Coding) 是一个能大幅度提高前端开发效率的工具，能够实现 HTML、CSS 的快速编写。**

> **快速生产HTML结构语法**
>
> - 生成标签按`tab`键，比如div，然后tab
>
> - 多个相同标签可以： `div*3`
>
> - 父子级标签可以：`ul>li*4`
>
> - 兄弟级标签：`div+p`
>
> - 带有类名或id名的，直接`.class` 或者` #id`
>
>   ```html
>   <!-- (.foo>h1)+(#bar>h2)+ui>li.item$*3 生成如下代码：-->
>   <div class="foo">
>       <h1></h1>
>   </div>
>   <div id="bar">
>       <h2></h2>
>   </div>
>   <ui>
>       <li class="item1"></li>
>       <li class="item2"></li>
>       <li class="item3"></li>
>   </ui>
>   ```
>
> **快速生成CSS语法**
>
> - 简写按tab键生成

### CSS元素显示模式

**元素（标签）以什么方式进行显示；比如<div>占一行、一行可以放多个<span>。HTML元素一般分为`块元素`和`行内元素`**

**块元素：**

- 常见有`<H1>~<H6>、<p>、<div>; <div>`最典型块元素
- 独占一行；高、宽及内外边距都可以控制
- 宽度默认是容器（父级宽度）的100%
- 是一个容器盒子，里面可以放行内或者块级元素

**行元素：**

- 常见有`<a>、<span>`等
- 相邻元素在一行上，一行可以放多个；不能设置高度和宽度
- 行内元素只能容纳文本或其它行内元素（链接不能放链接，但<a>可以转换块级模式）

**行内块元素：**

- 同时具有块元素和行元素特别，比如`<img/>、<input/>、<td/>`
- 一行显示多个（有空白间隙），可以设置高、宽及内外边距

**元素显示模式转换：**

一个模式元素需要另外一种模式的特性；比如想要增加<a>的触发范围：

```html
<style>
  /* 行内元素转换块级元素（block）*/
  a {
    width:30px;
    height:40px;
    display: block;
  }
  /* 块级元素转行内元素（inline） 或 行内块元素（inline-bolock）*/
  div {
    width:100px;
    height:120px;
    display: inline;
 	display: inline-bolock;
</style>
<a href="#">增大链接选择范围</a>
<div> 1 </div> <div> 2 </div>
```

### CSS的背景

```html
<style>
  .div { 
    /* 1.定义背景颜色 */
    background-color: green;
    /* 2.定义背景图片：便于控制位置*/
    background-image: url(image/img.png);
    /* 3.背景图片平铺属性：非平铺 */
    background-repeat: no-repeat; 
    /* 4.背景图片位置：（x y)可以是方位或数值 */   
	background-position: center top;
    background-position: 30px 20px;
    background-position: 15px center;
    /* 5.背景图片固定： */
    background-attachment: fixed || scroll;
    /* 合并写法*/
    background: green url(img.png) no-repeat fixed 15px center;
    /* 6. 盒子透明设置 */
    background: rgba(0,0,0,0.3);
  }
</style>
```

### CSS三大特性

**层叠性：**

相同选择器给设置相同的样式，此时一个样式机会`覆盖（层叠）`另一个`冲突（就近原则）`的样式

**继承性：**

子标签继承父标签某些（文本、色号等）样式，比如 div>p ,div样式修改会影响p标签

**优先级：**

选择器相同，按层叠性执行

选择器不同，根据选择器权重

- 继承或者*  0,0,0,0
- 元素选择器 0,0,0,1
- 类选择器、伪类选择器 0,0,1,0
- ID选择器 0,1,0,0
- 行内样式 style="" 1,0,0,0
- !important  ∞无穷大（最高权重）

复合选择器存在权重叠加的问题

```html
<style>
  li {
      /*0,0,0,1*/
      text-anchor: middle;
  }
  ul li{
      /*0,0,0,1+0,0,0,1*/
      text-anchor: start;
  }
</style>
<ul>
  <li>a</li>
</ul>
```





### CSS3新增选择器

[参考](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Selectors)

#### 属性选择器

可以根据元素特定[属性](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Attribute_selectors)来选择元素；`类选择器、属性选择器、伪类选择器权重都是10`

```html
<style>
  /* 必须是input 但是同时具有 value这个属性 选择这个元素  [] */
  /* input[value] {
  color:pink;
  } */
  /* 只选择 type =text 文本框的input 选取出来 */
  input[type=text] {
    color: pink;
  }
  /* 选择首先是div 然后 具有class属性 并且属性值 必须是 icon开头的这些元素 */
  div[class^=icon] {
    color: red;
  }
  section[class$=data] {
    color: blue;
  }
  div.icon1 {
    color: skyblue;
  }
  /* 类选择器和属性选择器 伪类选择器 权重都是 10 */
</style>
<!-- 1. 利用属性选择器就可以不用借助于类或者id选择器 -->
<!-- <input type="text" value="请输入用户名">
<input type="text"> -->
<!-- 2. 属性选择器还可以选择属性=值的某些元素 重点务必掌握的 -->
<input type="text" name="" id="">
<input type="password" name="" id="">
<!-- 3. 属性选择器可以选择属性值开头的某些元素 -->
<div class="icon1">小图标1</div>
<div class="icon2">小图标2</div>
<div class="icon3">小图标3</div>
<div class="icon4">小图标4</div>
<div>我是打酱油的</div>
<!-- 4. 属性选择器可以选择属性值结尾的某些元素 -->
<section class="icon1-data">我是安其拉</section>
<section class="icon2-data">我是哥斯拉</section>
<section class="icon3-ico">哪我是谁</section>
```

#### 结构伪类选择器

主要根据[文档结构](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Pseudo-classes)来选择元素，常用于根据父级选择子元素

```html
<style>
  /* 1. 选择ul里面的第一个孩子 小li */
  ul li:first-child {
    background-color: pink;
  }
  /* 2. 选择ul里面的最后一个孩子 小li */
  ul li:last-child {
    background-color: pink;
  }
  /* 3. 选择ul里面的第2个孩子 小li  <b>nth-child还能跟公式</b> */
  ul li:nth-child(2) {
    background-color: skyblue;
  }
  /* 4.把所有的偶数 even的孩子选出来 */
  ul li:nth-child(even) {
    background-color: #ccc;
  }
  /* 5.把所有的奇数 odd的孩子选出来 */
  ul li:nth-child(odd) {
    background-color: gray;
  }
  /* 6.从第3个开始选择所有 */
  ul li:nth-child(n+3) {
  background-color: pink;
  }
   /* 7.选择前3个 */
  ul li:nth-child(-n+3) {
    background-color: pink;
  }
</style>
<ul>
  <li>我是第1个孩子</li>
  <li>我是第2个孩子</li>
  <li>我是第3个孩子</li>
  <li>我是第4个孩子</li>
  <li>我是第5个孩子</li>
  <li>我是第6个孩子</li>
  <li>我是第7个孩子</li>
  <li>我是第8个孩子</li>
</ul>
```

`nth-child(n)` 和 `nth-of-type(n)`	区别：

- section div:nth-child(2)：会把所有元素都排序号，先选择n的序号再看序号前面元素
- section div:nth-of-type(2)：会把指定的元素(div)排序，然后选择n的序号再看序号前面元素

#### 伪元素选择器

可以利用CSS创建新标签元素

| 选择符    | 简介                                        |
| --------- | ------------------------------------------- |
| ::berfore | element::berfore{} 在元素内部的前面插入内容 |
| ::after   | element::after{} 在元素内部的后面插入内容   |

** `before`和`after`创建一个元素（在文档树中是找不到的），是属于行内元素；必须要有`content	`属性，权重为1**

```html
<style>
  div {
    width: 200px;
    height: 200px;
    background-color: pink;
  }
  /* div::before 权重是2 */
  div::before {
    /* 这个content是必须要写的 */
    /* display: inline-block; */
    content: '我';
    /* width: 30px;
    height: 40px;
    background-color: purple; */
  }
  div::after {
    content: '小猪佩奇';
  }
</style>
<div>
  是
</div>
```

