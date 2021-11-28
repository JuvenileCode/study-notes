CSS 移动布局

### 理想视口 - meta标签

```html
<!-- 定义meta标签 -->
<meta name="viewport" content="width=device-width, user-scalable=no,initial-scale=1.0,maximum-scale=1.0,minimum-scale=1.0" />
```

### 二倍图

**物理像素&物理像素比**

PC端页面：1个px等于1个物理像素，但移动端不尽相同（比如iPhone8：pc 1px = 2px）

一个px能显示的物理像素点的个数，称为物理像素比或屏幕像素比

#### background-size 背景图

```css
/* background-size: 图片的宽度 图片的高度; */
background-size: 500px 200px;
/* 1.只写一个参数 肯定是宽度 高度省略了  会等比例缩放 */
background-size: 500px;
/* 2. 里面的单位可以跟%  相对于父盒子来说的 */
background-size: 50%;
/* 3. cover 等比例拉伸 要完全覆盖div盒子  可能有部分背景图片显示不全 */
background-size: cover;
/* 4. contain 高度和宽度等比例拉伸 当宽度 或者高度 铺满div盒子就不再进行拉伸了 可能有部分空白区域 */
background-size: contain;
```

### CSS初始化

移动端CSS初始化推荐使用[normalize.css](https://necolas.github.io/normalize.css/8.0.1/normalize.css)

**css3盒子模型及一些样式：**

```css
/* 传统盒子模型会 padding 和 border 会撑大盒子*/
box-sizing: content-box;
```

```css
/* css3(box-sizing: border-box) padding 和 border 不会再撑大盒子了 */
box-sizing: border-box;
/* 清除点击高亮，设置透明*/
a {-webkit-tap-highlight-color: transparent;}
/* 加上这个属性才能给按钮和输入框自定义样式 */
input {-webkit-appearance: none;}
/* 禁用长按页面时的弹出菜单*/
img,a{-webkit-touch-callout: none;}
```

### flex布局

**flex意为弹性布局，任何一个容器都可以指定flex布局。**

- 父盒子设为flex布局后，子元素的float,clear和vertical-align属性失效
- 采用flex布局的元素，称为flex容器；它所有子元素自动成为容器成员
- flex布局分为主轴和侧轴（行和列，x轴和y轴）两个方向

#### Flex布局父项常见属性

```
flex-direction: 设置主轴方向
justify-content: 设置主轴上的子元素排列方式
flex-wrap: 设置子元素是否换行 (flex布局默认不换行)
align-content: 设置侧轴上的子元素出现换行的排列方式（多行）
align-items: 设置侧轴上的子元素排列方式（单行）
flex-flow: 复合属性,类同时设置flex-direction和flex-wrap
```
**示例**

```css
div {
  /*给父盒子添加flex*/
  display: flex;
  /*默认主轴是X轴方向, 可以忽略*/
  flex-direction: row;
  /*设置主轴上子元素排列方式*/
  justify-content: center;
  /*设置侧轴子元素排列方式 （justify-content: center + align-items:center 水平且垂直居中）*/
  align-items:center;
  /*flex布局默认不换行*/
  flex-wrap: wrap;
  /*设置主轴方向和换行 简写*/
  flex-flow: column wrap;
}
```

#### Flex布局子项常见属性

```
flex 定义子项目分配剩余空间
align-self 子项自己在侧轴的排列方式
order 子项排列的前后顺序
```

### rem布局

#### rem基础

**rem(root em)是一个相对单位，类似于em(em相对于父元素字体大小)；不同的是rem的基准是相对html的元素大小。**

```css
/* 例：根元素(html)设置font-size=12px，非根元素width:2rem，则px = 24px */
html {
  font-size: 14px;
}
div{
  font-size: 12px;
}
p{
  /* em 相对于父元素的字体大小 10*12=120px*/
  width: 10em;
  height: 10em;
  /* rem相对于 html 元素 字体大小 12*14=168px*/
  width: 12rem;
  height: 12rem;
}
```

#### 媒体查询

**媒体查询（Media Query）是CSS3的新语法**

使用@media可以针对不同的媒体类型（屏幕尺寸）定义不同的样式

