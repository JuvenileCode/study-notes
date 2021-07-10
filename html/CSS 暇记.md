## CSS 暇记

[CSS参考手册在线查询](https://css.doyoe.com/)

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

<!-- 2. 子选择器: 只选择某元素最近一级子元素；元素用>隔开； -->
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



