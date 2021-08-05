## CSS 进阶

### 精灵图

一个网页中往往会应用很多小的背景图作为修饰，为了减少服务器交互次数提高页面加载速，出现了`CSS精灵技术(CSS Sprites、CSS雪碧)`

**使用场景：**多用于背景图

**实现原理：**使用`background-position` 移动背景图片位置（[在线工具](https://www.spritecow.com/)目标图片的x和y坐标）

```html
<style>
  .box1 {
    background: url('img/sprites.png') no-repeat 0 -461px;
    width: 236px;
    height: 107px;
  }
  .box2 {
    background: url('img/sprites.png') no-repeat 0 -461px;
    width: 236px;
    height: 107px;
  }
  /* ---------------------- */
  .pinyin span {
    display: inline-block;
    background: url('img/abcd.jpeg') no-repeat;
  }
  .pinyin span:nth-of-type(1) {
    background-position: -360px 0;
    width: 104px;
    height: 120px;
  }
  .pinyin span:nth-of-type(2) {
    background-position: -120px -8px;
    width: 107px;
    height: 112px;
  }
</style>
<div class="box1"></div>
<!-- ====== -->
<div class="pinyin">
    <span></span>
    <span></span>
</div>
```

### 字体图标

[字体图标下载](https://icomoon.io/app/#/select)

**使用场景：**网页中通用、常用的一些小图标

**为何不用精灵图，而要用字体图标（`iconfont`）？**

1. 图片文件比较大
2. 图片本身放大和缩小会失真
3. 一旦图片制作完毕想要更换非常复杂

字体图标展示的是图标，本质属于`字体`

- 轻量级：图标字体比图像少
- 灵活性：本质是文字，可以改大小颜色、阴影、透明效果等
- 兼容性好：但不能替代精灵图技术，对工作中图标部分的优化

```html
<style>
  /*字体图标声明 从icomoon网站选取并自动生成*/
  @font-face {
    font-family: 'icomoon';
    src: url('fonts/icomoon.eot?cmt4ai');
    src: url('fonts/icomoon.eot?cmt4ai#iefix') format('embedded-opentype'),
      url('fonts/icomoon.ttf?cmt4ai') format('truetype'),
      url('fonts/icomoon.woff?cmt4ai') format('woff'),
      url('fonts/icomoon.svg?cmt4ai#icomoon') format('svg');
    font-weight: normal;
    font-style: normal;
    font-display: block;
  }
  
  span {
    font-family: 'icomoon';
    font-size: 50px;
    color: red;
  }
</style>
<span></span>
```

### CSS三角

```html
<style>
  .box2{
    width: 0;
    height: 0;
    /* 边框全部设置为透明 */
    border: 10px solid transparent;
    /* 三角在哪边就在哪边设置颜色 */
    border-left-color: #00a4ff;
  }
</style>
```

### CSS用户界面样式

**更改用户的鼠标样式**

```html
<ul>
    <li style="cursor: default">默认鼠标样式</li>
    <li style="cursor: pointer">小手样式</li>
    <li style="cursor: move">移动样式</li>
    <li style="cursor: text">文本样式</li>
    <li style="cursor: not-allowed">禁止样式</li>
</ul>
```

**表单轮廓**

```css
input {
  /*取消轮廓线*/
  outline: none;
}
```

**防止表单拖拽**

```css
textarea {
  resize: none;
}
```

### vertical-align

`vertical-align`经常用于设置图片或者表单（行内块元素）和文字垂直对齐

`vertical-align` 只对行内元素、表格单元格元素生效：不能用它垂直对齐块级元素

```html
<style>
 /* 文字和图片居中对齐 */
.middle { vertical-align: middle; }
 /* 解决图片底部默认留白缝隙问题 */
  img {vertical-align: middle | top |bottom}
</style>
<div>An <img class="middle" src="https://mdn.mozillademos.org/files/12245/frame_image.svg" alt="link" width="32" height="32" /> image with a middle alignment.</div>
```

### 溢出省略号显示

**单行文本溢出省略号**

```html
<style>
  .text-hh {
    width: 150px;
    height: 60px;
    background-color: #8ce6b0;
    /*文字显示不允许换行*/
    white-space: nowrap;
    /*超出部分隐藏*/
    overflow: hidden;
    /*超出的部分用省略号显示*/
    text-overflow: ellipsis;
  }
</style>
<div class="text-hh">
  为什么都说童年是无忧无虑最快乐的？但是童年的时候你自己却只感到作业太多想快点长大
</div>
```

**多行文本溢出省略号**

```html
<style>
  .text-list-hh {
    width: 150px;
    height: 45px;
    background-color: #8ce6b0;

    /*超出部分隐藏*/
    overflow: hidden;
    /*超出的部分用省略号显示*/
    text-overflow: ellipsis;
    /* 存在兼容性问题，需要严格控制大小 */
    display: -webkit-box;
    -webkit-line-clamp:2;
    -webkit-box-orient: vertical;
  }
</style>
<div class="text-list-hh">
  为什么都说童年是无忧无虑最快乐的？但是童年的时候你自己却只感到作业太多想快点长大
</div>
```

