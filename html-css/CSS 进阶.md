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



