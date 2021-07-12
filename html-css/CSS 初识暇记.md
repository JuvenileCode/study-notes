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
