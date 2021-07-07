## Html 重修暇记

### HTML

#### Web标准

web标准主要位`结构`，`表现`，`行为` ；提倡分离，简单来说：

- 结构 =》 HTLM
- 表现 =》CSS
- 行为 =》JavaScript

### HTML 标签

#### 标题｜段落｜换行

```html
标题： <H1></H1> ... <H6></H6>
段落： <p> </p>
换行： <br/>
```

#### 文本格式化标签

```html
<!--突出语句重要性,都推荐使用前者（语义强烈）-->
加  粗：<strong></strong> 或<b></b>
倾  斜：<em></em> 或<i></i>
删除线：<del></del> 或<s></s>
下划线：<ins></ins> 或<u></u>

```

#### div｜span 标签

```html
<!-- div 和 span 是一个盒子，用来装饰内容的 -->
<div> div是division的缩写,表分割、分区。*独占一行 </div>
<span> span为跨度，跨距。*可以一行多个 </span>
```

#### 图像标签

```html
<!--src 是 img 的属性-->
<img src="图像url" alt="图像不显示替换文本" title="提示文本" 
     width="高度" height="宽度" border="边框"/>
```

#### 超链接标签

```html
<a href="跳转目标" target="目标窗口弹开方式">文本或图像</a>
<!--target="_self(当前窗口) | _blank(新窗口)"-->
<a href="#">空连接</a> 
<!--锚点连接，页面内定位 -->
1、<a href="#two">目录2</a>  2、<h3 id="two">目录2</h3>
```







