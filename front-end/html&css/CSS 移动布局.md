## CSS 移动布局

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

