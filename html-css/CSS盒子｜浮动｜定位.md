## CSS盒子｜浮动｜定位

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

当父元素没设置足够大小的时候，而子元素设置了浮动的属性，子元素就会跳出父元素的边界（脱离文档流），尤其是当父元素的高度为auto时，而父元素中又没有其它非浮动的可见元素时，父盒子的高度就会直接塌陷为零， 我们称这是CSS高度塌陷

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

### CSS3新特性

#### CSS3盒子模型

CSS3中可以通过`box-sizing`来指定盒子模型的大小计算方式

1. `box-sizing:content-box` 盒子大小为width+padding+border
2. `box-sizing:border-box` 盒子大小为width

```html
<style>
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
  div {
    width: 200px;
    height: 200px;
    background-color: pink;
    border: 20px solid red;
    padding: 15px;
    box-sizing: content-box;
  }
  p {
    width: 200px;
    height: 200px;
    background-color: pink;
    border: 20px solid red;
    padding: 15px;
    /* css3 盒子模型  盒子最终的大小就是 width  200 的大小 */
    box-sizing: border-box;
  }
</style>
<div> one div</div>
<p> one p </p>
```

#### CSS3滤镜filter

filter CSS属性（filter：函数）将模糊或颜色偏移等图形效果应用于元素

```html
<style>
  img {
    /* blur是一个函数 小括号里面数值越大，图片越模糊 注意数值要加px单位 */
    filter: blur(15px);
  }
  img:hover {
    filter: blur(0);
  }
</style>
<img src="ima/video.jpeg" alt="">
```

#### CSS3 calc函数

calc()`width:calc(100%-80px)`此CSS函数让你在声明CSS属性值时执行一些计算

 ```html
 <style>
   .father {
     width: 300px;
     height: 200px;
     background-color: pink;
   }
   .son {
     /* width: 150px; */
     /* width: calc(150px + 30px); */
     width: calc(100% - 30px);
     height: 30px;
     background-color: skyblue;
   }
 </style>
 <!-- 需求我们的子盒子宽度永远比父盒子小30像素 -->
 <div class="father">
   <div class="son"></div>
 </div>
 ```

#### CSS3过度transition

过度`transition`是从一个状态渐渐的过渡到另外一个状态，搭配`:hover`使用

> transition:属性 花费时间 运动曲线 何时开始

```html
<style>
  div {
    width: 200px;
    height: 100px;
    background-color: pink;
    /* transition: 变化的属性 花费时间 运动曲线 何时开始; */
    /* transition: width .5s ease 0s, height .5s ease 1s; */
    /* 如果想要写多个属性，利用逗号进行分割 */
    /* transition: width .5s, height .5s; */
    /* 如果想要多个属性都变化，属性写all就可以了 */
    /* transition: height .5s ease 1s; */
    /* 谁做过渡，给谁加 */
    transition: all 0.5s;
  }
  div:hover {
    width: 400px;
    height: 200px;
    background-color: skyblue;
  }
</style>
<div> </div>
```

#### transform

##### 2D转换transform

```css
<style>
/*移动盒子的位置： 定位 ｜ 盒子的外边距 ｜ 2d转换移动*/
.tr {
  width: 200px;
  height: 200px;
  background-color: blue;
  transform: translate(100px, 100px);
  /* 旋转 */
  transform: rotate(45deg);
}
</style>
```

translate沿着X和Y轴移动，最大优点是不会影响其它元素位置（对行内标签没效果）

**旋转:**

```css
.rotate-test {
  margin-top: 30px;
  width: 100px;
  height: 100px;
  background-color: #d2dddd;
  /*border-radius: 50%;
  border: 5px solid cornflowerblue;*/
  transition: all 3s;
}
.rotate-test:hover {
  transform: rotate(360deg);
}
```

##### 2D转换translate-origin

可以设置元素转换的中心点`transform-origin: x y`

```css
/* 可以跟方位名词,也可以是px像素单位 */
tranform-origin: left bottom;
```

##### 2D转换缩放scale

对比`widht | heigth`：不会影响其它盒子，可以设置缩放中心点

```css
transform:scale(x,y) /*x,y是倍树(<1是缩小｜>1是放大) 不带单位*/
.scale-test{
  width: 200px;
  height: 200px;
  background-color: #2baee9;
  transition: all 1s;
}
.scale-test:hover{
  transform: scale(0.5,0.5);
}
```

#### CSS3动画

制作动画：1. 定义动画   2. 调用

**动画序列**

- 0%是动画的开始，100%是动画的完成
- 在`@keyframes`中规定某项CSS样式，可以改变任意多样式任意多次数
- 请用百分比来规定变化发生的时间，或用关键词`from`和`to`等同于0%和100%

```css
/*定义动画*/
@keyframes move-all {
  /*多个状态的变化*/
  0% {
    transform: translate(0, 0);
  }
  25% {
    transform: translate(900px, 0);
  }
  50% {
    transform: translate(900px, 300px);
  }
  75% {
    transform: translate(0, 300px);
  }
  100% {
    transform: translate(0, 0);
  }
}
/*使用动画*/
.animation-2 {
  width: 150px;
  height: 150px;
  background-color: #589f88;
  /*调用自定义动画*/
  animation-name: move-all;
  /*持续时间*/
  animation-duration: 3s;
  /*动画重复次数 infinite(无限)*/
  animation-iteration-count: 3;
}
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

### 元素的显示与隐藏

让一个元素在页面中隐藏或者显示出来

1. display 显示隐藏 （**display隐藏元素后不再占有原来的位置**）

   1.1  `display:none` 隐藏对象

   1.2  `display:block` 除了转换块级元素，同时还有显示元素的意思

2. visibility 显示隐藏（**visibility隐藏元素后继续占有原来的位置**）
   2.1  `visiblity:visible` 元素可视
   2.2  `visibility:hidden` 元素隐藏

3. overflow 溢出显示隐藏 （有定位的盒子慎用hidden，它会隐藏多余的部分）
   3.1 `visible | hedden | scroll | auto`  默认全显示 | 溢出部分隐藏 | 始终添加滚动条 | 溢出后添加滚动条

### PS切图

**PS常见的切图有：图层切图、切片切图、PS插件切图等**

- **图层切图：** 右击图层 ---> 快速导出PNG

  但是很多情况下需要合并图层在导出：

  选中需要的图层，图层菜单 ---> 合并图层(ctrl+e)  右击 --->快速导出PNG

- **切片切图：**

  利用切片工具手动划出

  文件菜单--->导出--->存储web设备所用格式--->选择图片格式--->存储

- **PS插件切图：**

  Cutterman是一款运行中Photoshop的插件

### CSS属性书写顺序

1. 布局定位属性： display/position/float...
2. 自身属性：width/height/margin/padding...
3. 文本属性：color /font /text-decoration...
4. 其它属性(CSS3)：content/cursor/...

### 定位

1. 浮动可以让多个块级盒子一行没有缝隙排列显示，经常用于横向排列盒子

2. 定位则是可以让盒子自由的在某个`盒子內移动位置`或者`固定屏幕中某个位置`；并且可以压住其它盒子

**定位 = 定位模式 + 边偏移**

**定位模式：**

通过CSS的`position`属性来设置，有四个值：

```json
{"static":"静态定位","relative":"相对定位","absolute":"绝对定位","fixed":"固定定位"}
```

**边偏移：**

用于确定盒子最终位置；有`top`,`bottom`,`left`和`right`。

#### 静态定位

1. 默认的定位方法，无定义的意思 `<!-- 选择器 {position: static;} -->`

2. 静态定位按标准流特性摆放位置，它没有边偏移；布局很少用到。

#### 相对定位

1. 在元素移动位置，相对它原来的位置 `<!-- 选择器 {position: relative;} -->`

2. 原来标准流位置继续占用，不脱标

```html
<style>
  .box1 {
    margin: 0 auto;
    position: relative;
    top: 100px;
    left: 100px;
    width: 200px;
    height: 200px;
    background-color: #ff6700;
  }
  .box2 {
    margin: 0 auto;
    width: 200px;
    height: 200px;
    background-color: #47cb89;
  }
</style>
<div class="box1"></div>
<div class="box2"></div>
```

所以，相对定义用于辅助绝对定位

#### 绝对定位

元素在移动位置时现对祖先元素来定位

1. 如果没有父元素或者父元素没有定位，则以浏览器为标准定位
2. 如果父元素有定位（相对｜绝对｜固定），则以最近一级有定位的父元素为参照
3. 绝对定位不占有原位置，脱标（浏览器把它视作块级元素）

```html
<style>
  .on {
    position: relative;
    width: 560px;
    height: 560px;
    background-color: #e6fffb;
  }
  .on-one {
    width: 360px;
    height: 360px;
    background-color: #adc6ff;
  }
  .absolute-one {
    position: absolute;
    left: 30px;
    top: 20px;
    width: 120px;
    height: 120px;
    background-color: #e6f7ff;
  }
</style>
<div class="on">
    <div class="on-one"></div>
    <div class="absolute-one"></div>
</div>
```

**子绝父相：**

定位中最常用的一种方式：子级是绝对定位，父级要用相对定位

1. 子级绝对定位，不会占有位置，可以放到父盒子里的任何位置
2. 父盒子需要加定位限制子盒子在父盒子內显示

#### 固定定位

**固定于浏览器可视区域。浏览器滚动元素位置不会变**

1. 以浏览器可视窗口为参照点移动

2. 固定定位不占有原先位置，脱标；可以看做一种特殊的绝对定位

```html
<style>
  .fixed {
    position: fixed;
    top: 30px;
    right: 30px;
  }
</style>
<div class="fixed">
    <em style="font-size: 18px;color: #47cb89"> 广告位招租 </em>
</div>
```

**固定定位小技巧：固定版心右侧位置**

1. 固定定位盒子left:50%，走到浏览器可视区（版心）的一半位置
2. 固定定位盒子margin-left:版心宽度的一半距离。

```html
<style>
  .w {
    width: 800px;
    height: 1400px;
    background-color: pink;
    margin: 0 auto;
  }
  .fixed {
    position: fixed;
    /* 1. 走浏览器宽度的一半 */
    left: 50%;
    /* 2. 利用margin 走版心盒子宽度的一半距离 */
    margin-left: 405px;
    width: 50px;
    height: 150px;
    background-color: skyblue;
  }
</style>
<div class="fixed"></div>
<div class="w">版心盒子</div>
```

#### 粘性定位

**粘性定位可以被认为是相对定位和固定定位的混合** `{position: sticky; top:10px}`

1. 以浏览器的可视窗口为参照点移动（固定定位特性）
2. 粘性定位占有原先的位置（现对定位特性）
3. 必须添加top、left、right、bottom其中一个才有效

#### 定位叠放顺序

在使用布局时，可能出现盒子重叠的情况；可以用`z-index`来控制盒子的前后次序（z轴）。

```javascript
选择器 {z-index:1;}
```

1. 数值可以是正整数，负整数或0，默认是auto，数值越大，盒子越靠上

2. 如果属性相同，则按书写顺序排
3. 只有定位的盒子才有z-index属性

```html
<style>
  .box {
    position: absolute;
    top: 0;
    left: 0;
    width: 200px;
    height: 200px;
  }
  .xiongda {
    background-color: red;
    z-index: 1;
  }
  .xionger {
    background-color: green;
    left: 50px;
    top: 50px;
    z-index: 2;
  }
  .qiangge {
    background-color:blue;
    left: 100px;
    top: 100px;
  }
</style>
<div class="box xiongda">熊大</div>
<div class="box xionger">熊二</div>
<div class="box qiangge">光头强</div>
```

#### 绝对定位居中

加了绝对定位的盒子不能通过`margin:0 auto`水平居中

```html
<style>
  .box {
    width: 300px;
    height: 250px;
    position: absolute;
    /*垂直居中*/
    left: 50%;
    margin-left: -150px;
    /*水平居中*/
    top: 50%;
    margin-top: -125px;
    /* 绝对定位的盒子设置margin无效
    margin: 0 auto;*/
    background-color: #a0d911;
  }
</style>
```

#### 拓展

- 绝对定位（固定定位）会完全压住盒子
- 浮动元素只会压住标准流盒子，但不会压住标准流盒子里的文字或图片

### 网页布局总结

通过盒子模型，清楚知道大部分html标签是一个盒子

通过CSS浮动、定位可以让每个盒子排列成为网页

**标准流**

可以让盒子上下或者左右排列，`垂直的块级盒子显示就用标准流布局`

**浮动**

可以让多个块级元素一行显示或者左右对其盒子，`多个盒子水平显示就用浮动布局`

**定位**

定位最大的特点是有层叠的概念，可以让多个盒子前后叠压来显示，`如果元素自由在某个盒子內移动就用定位布局`

