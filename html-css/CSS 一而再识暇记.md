## CSS 一而再识暇记

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
  }
</style>
<div class="div-read"></div>
```

