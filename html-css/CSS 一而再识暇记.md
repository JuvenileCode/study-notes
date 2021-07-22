##  CSS 一而再识暇记

### 盒子模型

#### 边框（border)

border设置元素边框，边框粗细、样式、颜色

```html
<style>
  .div-read {
    background-color: #adc6ff;
    width: 300px;
    height: 100px;
    /*或者： border: 5px solid red*/
    border-width: 5px;
    border-style: solid;
    border-color: greenyellow;
    /*单独设置上边框属性，也可以下，左，右边框设置*/
    border-top: darksalmon dotted 3px;
    /*合并相邻的边框 */
    border-collapse: collapse;
  }
</style>
```

#### 内边距

```html
<style>
  .div-padding {
    /*或者：padding: 3px 2px 3px 5px; 值数量不一样，代表设置不同 */
    padding-left: 3px;
    padding-right: 3px;
    padding-top: 3px;
    padding-bottom: 4px
  }
  /* 如果没有设置高度或宽度，则padding不会影响原有大小 */
</style>
```

**border、padding 会影响盒子原本大小（撑大盒子）**

#### 外边距

```html
<style>
  /* margin和padding类似；margin: 2px 3px 3px 5px */
  .div-margin {
    width: 200px;
    height: 200px;
    background-color: pink;
  }
  .one {
    margin-bottom: 20px;
  }
  .two {
    margin-top: 20px;
  }
</style>
<div calss="one"></div>
<div class="two"></div>
```

#### 外边距合并塌陷

```html
<style>
  /* 清除内外边距 */
  * {
    margin: 0;
    padding: 0;
  }
  /*字父盒子塌陷问题*/
  .ho {
    width: 300px;
    height: 250px;
    background-color: #2d8cf0;
    margin-top: 20px;
    /*解决1：给父元素定义上边框*/
    border: 1px solid transparent;
    /*解决2：给父元素定义內边距*/
    padding: 1px;
    /*解决3(推荐)：overflow: hidden*/
    overflow: hidden;
  }
  .hw {
    width: 150px;
    height: 120px;
    background-color: #f7b84f;
    /*子盒子设置top会将父盒子也下移*/
    margin-top: 50px;
    
    
  }
</style>
<div class="ho">
  <div class="hw"></div>
</div>
```

#### 圆角边框

```html
<style>
    .box {
        /* length越大 圆角度越大 */
        border-radius: 10px;
    }
    .round {
        width: 200px;
        height: 200px;
        background-color: darkseagreen;
        margin: 20px auto;
        /* 设置圆 */
        border-radius: 50%;
    }
    .rounded-rectangle {
        width: 300px;
        height: 130px;
        background-color: lavenderblush;
        margin: 20px auto;
		/* 设置圆角矩形 */
        border-radius: 65px;
    }
</style>
<div class="round"></div>
<div class="rounded-rectangle"></div>
```

#### 盒子阴影

```html
<style>
    .shadow {
        /* 
        h-shadow	必需的。水平阴影的位置。允许负值
        v-shadow	必需的。垂直阴影的位置。允许负值
        blur	可选。模糊距离
        spread	可选。阴影的大小
        color	可选。阴影的颜色。在CSS颜色值寻找颜色值的完整列表
        inset	可选。从外层的阴影（开始时）改变阴影内侧阴影 
        */
        width: 300px;
        height: 200px;
        background-color: lightblue;
        margin: 20px auto;
    }
    .shadow:hover {
        box-shadow: 10px 10px 15px 5px #cccccc ;
    }
</style>
<div class="shadow"></div>
```

#### 文字阴影

```html
<style>
  text-shadow {
    text-shadow: 5px 5px 6px #cccccc;
  }
</style>
```

### 浮动

#### 介绍

一个完整的网页布局由标准流、浮动、定位共同布局完成

**网页布局第一准则：多个块级元素纵向排列找`标准流`，多个块级元素横向排列找`浮动`**

```html
<!-- none | left | right -->
<style>
  .tf{
    float: left;
  }
</style>

```

**浮动的特性：**

1. 浮动元素会脱离标准流；不再保留原先的位置
2. 浮动元素会一行內显示并且元素顶部对其；撑开会换行显示
3. 浮动元素会具有`行内块`元素的特性

**浮动元素经常和标准流父级搭配使用**

- 网页布局第一准则：先用标准流的父元素排列上下位置，内部子元素采取浮动排列左右位置。

- 网页布局第二准则：先设置盒子大小，之后设置盒子的位置。

```html
<style>
  .box {
    width: 960px;
    height: 360px;
    background-color: #adc6ff;
    margin: 0 auto;
  }
  .left {
    float: left;
    width: 180px;
    height: 360px;
    background-color: #57a3f3;
  }
  .right {
    float: left;
    width: 780px;
    height: 360px;
    background-color: #47cb89;
  }
</style>
<div class="box">
    <div class="left">左侧</div>
    <div class="right">右侧</div>
</div>
```

**注意：**

1. 浮动和标准流的父盒子搭配（标准流父元素排列上下位置，内部子元素浮动排列左右位置）
2. 标准流内部子盒子浮动，理论上其它盒子最好一起浮动

#### 清除浮动

**为什么需要清除浮动？**

由于父级盒子很多情况下不能确定高度（不设置高度会变为0），子盒子浮动不占有位置，会影响下面标准流盒子

清除浮动后，父级根据浮动子盒子自动检测高度

```html
<style>
  /* both 清除左右两侧的浮动 */
  .box {
    clear: both
  }
</style>
```

**清除浮动方法：**

1. 额外标签法（隔墙法），W3C推荐做法
2. 父级添加`overflow1`属性
3. 父级添加`after`伪元素
4. 父级添加双伪元素

```html
<style>
  /* 双伪元素清除浮动 */
  .clearfix:before,
  .clearfix:after {
    content: "";
    display: table;
  }
  .clearfix:after {
    clear: both;
  }
  .clearfix {
    *zoom: 1;
  }
  
  /* 伪元素清除浮动 */
  .clearfix:after {
    content: "";
    display: block;
    height: 0;
    clear: both;
    visibility: hidden;
  }
  .clearfix {
    *zoom: 1;
  }
  
  .top-one {
    margin: 30px auto 0;
    width: 400px;
    background-color: #e6f7ff;
    /*清除浮动：简单 无法显示溢出的部分*/
    overflow: hidden;
  }
  .one-a {
    width: 180px;
    height: 50px;
    background-color: #ffd77a;
    float: left;
  }
  .one-b {
    width: 180px;
    height: 80px;
    background-color: #ff6700;
    float: left;
  }
  /* 额外标签法（隔墙法），W3C推荐做法
   * 必须是块级元素、添加无意义标签
   */
  .clear-float {
    clear: both;
  }
  .overlapping {
    margin: 30px auto 0;
    width: 420px;
    height: 30px;
    background-color: #18b566;
</style>
<div class="top-one clearfix">
    <div class="one-a">one</div>
    <div class="one-b">two</div>
    <div class="clear-float"></div>
</div>
<div class="overlapping"> test</div>
```

### PS切图

**PS常见的切图有：图层切图、切片切图、PS插件切图等**

图层切图： 右击图层 ---> 快速导出PNG







