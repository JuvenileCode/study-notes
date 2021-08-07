## Html 复习暇记

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

#### 表格标签

```html
<!-- table:表格定义 th:表头 tr:行 td:列 表格属性后期基本用css设置-->
<!-- 结构化语义标签：thead=表格头部 tbody=表格内容  -->
<table align="center" border="1" >
  <thead>
    <tr> 
      <th> </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td> </td>
    </tr>
   </tbody>
</table>
<!--
合并单元格：
1.跨行合并：rowspan="2"
2.跨列合并：colspan="2"
-->
```

#### 列表标签

```html
<!-- 无序列表 <ul>标签只能放<li>标签,<li>标签可以使用其它标签-->
<ul>
  <li></li>
</ul>
<!-- 有序列表 ... -->
<ol>
  <li></li>
</ol>
<!-- 自定义列表 dd 是对 dt 的描述-->
<dl>
  <dt></dt>
  <dd></dd>
</dl>
```

#### 表单元素

```html
<!--表单由 表单域<form>、表单控件、提示信息3个部分组成-->
<form action="" method="get || post">
</form>
<!-- 1.input 输入表单元素-->
<input name="" type="text || password " value=""/>
<!-- 2.单选radio、多选checkbox-->
<input name="" type="radio" value=""/>
<input name="" type="checkbox" value=""/>
<!-- 3.表单提交、重置-->
<input type="submit" value="提交"/>  <input type="reset" value="清除"/>
<!-- 4.普通按钮、文件域-->
<input type="button" value="按钮" /> <input type="file" />
<!-- 5.label标签绑定表单元素(id和for值要一样)，自动追焦 -->
<label for="name">man</label>
<input type="text" name="name" id="name"/>
<!-- 6.select下拉元素 -->
<select id="">
  <option value="" select></option>
</select>
<!-- 7. textarea文本域 -->
<textarea rows="" cols="">
</textarea>
```

### HTML5新特性

#### HTML新增的语义化标签

header：头部标签

nav：导航标签

article：内容标签

section：定义文档某个区域

aside：侧边栏标签

footer：尾部标签

**注意：**

语义化标准主要针对搜索引擎，移动端比较适用



