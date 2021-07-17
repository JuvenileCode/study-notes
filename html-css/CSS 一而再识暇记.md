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

