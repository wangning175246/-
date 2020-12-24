https://www.w3school.com.cn/tags/tag_video.asp

#### html和xhtml

xhtml 是在原有的html做的一些语法规范。

#### html标签的属性

属性=“值” 标签有自己的一些属性来控制自己的样式和模式，如table的表格 border  cellpadding 

#### strong和b、em和i

strong和em标签都表示强调的标签，表现形态为文本的加粗和斜体，
b和i 标签同样表示文本的加粗和斜体

当一个div中有多个文字需要不同的样式的时候，我们可以使用上面的不同的标签进行区分

这样给不同的文字添加样式的时候，选择的时候也比较方便，不用给每个标签添加ID或者class

```html
<div>
    <i></i>
    <em></em>
    <b></b>
    <strong></strong>
    <span></span>
</div>
```

#### 语义标签

blockquote：引用大段的段落的标签

q：引用小段的段落的标签

abbr：缩写或者首字母缩写词

address：引用地址信息

cite：引用著作的标签。

#### img

alt： 图片加载是失效的时候，会提示的文字

title:图片的提示信息，鼠标移动到图片上面有提示

width,height 图片的大小，图片没有加载处理的时候大小已经固定，如果没有设置，网速慢的时候，图片会突然插入

如果父容器的宽度和高度设置死了，我想让图片撑满这个父容器，可以设置min-width,min-height

#### a

href 跳转到哪个地址，

​	锚定：#标签ID  可以跳转到指定的标签

target  ` _blank `新开窗口打开、` _self` 当前页打开

a标签中可以嵌套`img` 、文字

#### base

改变页面所有a标签的行为，一般会写在html文件的head部分

`base target="_blank "`

#### 特殊字符

`&lt`小于号

`&gt` 大于号

``&nbst` 空格

#### 表格

- table 表格的开始

  border表格的边框

  cellpadding 表的内边距

- td

  rowspan 这个单元格占几行

  colspan 这个单元格占用几列

- tr 表示一行

  align 表格这一列的文字的对其方式

  ​	center 居中对其

  ​	right  靠右对其 

#### 列表

#### 表单

##### form

​	action 提交到的具体地址

​	method 提交数据的方式

​	enctype 设置传输数据的类型

##### input 

用户用户输出信息 type属性可以有如下值

- text 普通文字

  disabled 

  placeholder  

- password 密码输入狂

- checkbox 复选框

​		checked 如果想让复选框的某一项默认选上，需要添加checked属性

- radio 单选框

​		name 属性，需要把需要单选的标签添加形同的name属性

- file 上传文件

  multiple  表示可以选择多个文件，进行上传

- submit 提交按钮

- reset 重置按钮

- pattern 正则验证

- autocomplete 自动提示，提示之前输入的内容 

- autofocus 获取焦点

- required 不能为空

##### email 电子邮件地址输入框,会进行内容的验证

##### url 网址输入框

##### number 数值输入框

##### range 滚动条 

##### date/month/week 日期控件

##### search 搜索框

##### color 颜色控件

##### tel 电话号码输入框

##### time 时间控件

##### textare 单行外部框

​	cols 表示文本框的列，大小也就是可以有多高

​	rows 表示文本框的行大小，可以有多宽

##### select option

- select 下拉菜单的开始

  size 每次显示几个option

  multiple 这个表示多选  ctrl+鼠标左键 

- option 表示每一个选项

  selected 表示当前默认被选中

  disabled 表示进行选择

##### fieldset 表单内元素进行分组

##### legend 为fieldset元素定义标题

##### optgroup 定义选项组

```html
<select name="选择" id="">
    <optgroup label="水果"></optgroup>
    <option value="">苹果</option>
    <option value="">苹果</option>
    <option value="">苹果</option>
    <optgroup label="蔬菜"></optgroup>
    <option value="">黄瓜</option>
    <option value="">黄瓜</option>
    <option value="">黄瓜</option>
</select>
```

​	![image-20200619163704020](.\images\html\image-20200619163704020.png)

```html
<fieldset>
    <legend>登录</legend>
    <form action="" method="post">
        <input type="text">
    </form>
</fieldset>
```

##### label

单选的时候选择 后面文字的时候，选择时无效的，我们给后面的文字加上label

```html
<form action="">
	<input type="radio" name="gender" id="man"><label for="man">男</label>
	<input type="radio" name="gender" id="woman"><label for="woman">女</label>
</form>
```

#### div span

div和span没有任何样式需要结合css使用

- div

  div 用来划分一个区域，相当于一个区域容器，可以容纳段落，标题，表格，图像等各种网页元素，html中大多数标签都可以嵌套在div标签中，div中可以嵌套div

- span 内联

  用来修饰文字的， 

#### iframe

在一个已有的页面中可以嵌套另外一个页面。

```go
<!-- scrolling 是否出现滚动态条 -->
<!-- frameborder是否有边框 -->
<!-- 当srcdoc和src同事存在的时候，只会识别srcdoc -->
<!-- srcdoc 中可以 添加html代码，或者纯文字内容 -->
<!-- <iframe  srcdoc="hello word" src="https://www.baidu.com" width="400px" height="400px" frameborder="0" scrolling=no></iframe> -->
<iframe src="https://www.baidu.com" frameborder="0" scrolling=no></iframe>
```

##### 作用

- 可以把iframe的宽和高设置100%，而后在页面的指定位置添加层，而后设置成我们自己的按钮，可以做一个钓鱼网站， 
- 局部代码刷新
- 共享代码
- 数据传输，iframe可以做一些跨域操作的，可以在src上去请求一些其他网站。
- 第三方的广告，我们只用留出广告的位置，具体的代码可以引用商户的自己的iframe.

#### br wbr

br就是换号，wbr软换行

wbr如果单词太长，可以把单词的部分换行，而后不是单词的整体进行换行

#### pre code

pre元素可以定义预格式化的文本，被pre包含的文本元素通常会保留空格和换行。

code 被code包含的内容，表示里面的内容是一段源代码。code里面代码需要进行字符的转移。

```html;
<pre>
<code>
	代码
</code>
</pre>
```

#### map area

给特殊图形添加链接，area能添加的热区域形状，矩形，圆形，多边型

area元素永远嵌套在map元素内部，area元素可以定义图像映射中的区域

可以给一个图片的指定区域添加热区域，也就点击图片的某个区域可以跳转链接，而不是这个图片。

map中的name顺序需要和图片的usemap的值相同

area

​	coords表示图形的坐标

​	shape表示是什么图形

​		circle 表示圆形 

​			coords 圆形坐标,圆的半径

​		rectangle 表示矩形

​			coord，左上点坐标,右下点坐标

​		polygon 表示多边型

​			coord 需要多变形的每个点的坐标，而后把所有点相连接就多边形的区域。

```html
<img src ="planets.gif" alt="Planets" usemap ="#planetmap" />

<map name="planetmap">
  <area shape="rect" coords="0,0,110,260" href="sun.htm" alt="Sun" />
  <area shape="circle" coords="129,161,10" href="mercur.htm" alt="Mercury" />
  <area shape="polygon"，coords="x1,y1,x2,y2,x3,y3,..." href="venus.htm" alt="Venus" />
</map>
```

#### embed object 

在网页中引入flash和一些其他的插件

embed object 可以对一些多媒体进行渲染的标签，如flash动画，插件等。

object  需要param配合使用。

#### audio video

audio 标签嵌入音频，

video表示嵌入视频

​	里面可以嵌套多个soruce，表示备用，如果第一个不能用了，会播放第二个

默认控件不显示，我们可以通过controls属性来显示控件，autoplay表示自动播放。loop循环播放

```html
<!DOCTYPE HTML>
<html>
<body>

<video src="/i/movie.ogg" controls autoplay loop>
</video>
<video>
    <source src="/i/movie.ogg" controls ></video>
</video>
</body>
</html>
```

#### 文字注解和方向 ruby rt bdo 

ruby标签定义ruby注释，rt标签定义字符(中文注音和字符)的解释或者发音

一般ruby和rt标签配合使用

<ruby>寒<rt>han</rt></ruby>

bdo 覆盖文件默认的方向

`<bdo dir="rtl">爱神的键</bdo>`

<bdo dir="rtl">爱神的键</bdo>

`<bdo dir="ltr">爱神的键</bdo>`

<bdo dir="ltr">爱神的键</bdo>

#### link

​	link都是添加早header头中

1. 网页标题上面的图标

   ```html
   <link rel="icon" type="image/png" sizes="16x16" href="/ui2019/logo-16-red.png">
   ```

2. DNS预解析

   ```html
   <link rel="dns-prefetch" href="//g.alicdn.com"/>
   ```

#### meta 

meta 添加 辅助信息，页面的元信息。

网站信息描述

```html
<meta name="description" content="您可以在淘寶網快速搜索女裝、男裝、鞋包、飾品、運動、家居家紡、手機数位、家電、美妝等各品類優質商品" />
```

设置网页关键字

```html
<meta name="keywords" content="淘宝,淘寶,淘寶網,掏寶,掏保,網上購物,集運,淘宝全球,taobao" />
```

针对浏览器双内核的渲染

```html
<meta name="renderer" content="webkit"/>
```

自动跳转页面

如何加载到指定的页面3s后会自动调整到后面的url,如果为空或者没写，则自动刷新页面

```html
<meta name="refresh" content="3" url="www.baidu.com">
```

设置缓存

```html
<meta http-equiv="pragma" content="no-cache">
<meta http-equiv="cache-control" content="no-cache">
<meta http-equiv="expires" content="Expires: Fri, 31 Dec 1999 16:00:00 GMT">
```

#### html5语义化标签

- header   页眉

- footer 页脚

- main 主题

- hgroup 标题组

- nav 导航

- article 独立的内容

- aside 辅助信息的内容

- section 区域

- figure 描述视频或者图片

- figcaption 描述视频或者图片的标题

- datalist 选项列表，可以intput 关联实现自动 提示

  ```html
  <section>
      <input type="text" list="elems">
      <datalist id="elems">
          <option value="a"></option>
          <option value="ab"></option>
          <option value="abcd"></option>
          <option value="apple"></option>
          <option value="abbr"></option>
          <option value="around"></option>
      </datalist>
  </section>
  ```

- details/summary 文档细节/文档标题

  open 属性表示默认是折叠打开的

  ```html
  <details open>
      <summary>HTML</summary>
      <p>内容</p>
  </details>
  ```

  <details open><summary>HTML</summary><p>内容</p></details>

- progress/meter 定义进度条/定义度量范围

  ```html
  <progress min=0 max="10" value=5></progress>
  ```

  <progress min=0 max="10" value=5></progress>

  ```html
  <meter min=0 max="10" value=5></meter>
  ```

  <meter min=0 max="10" value=5></meter >

- time 定义日期或者时间

- mark 带有标记的文本

  ```html
  今天是<mark>情人节</mark>,街上人很多
  ```

  今天是<mark>情人节</mark>,街上人很多

#### 表格扩展

添加单线 brod-collapse: collapse

隐藏空单元 empty-cells: hide

斜线分类 border/rotate

列分组 colgroup/col

#### 表单扩展

美化表单选择框，

​	伪类+背景图片+label

​	opacity+positon

<img src="C:\Users\18434\OneDrive\笔记\前端\image\html\image-20200619155754832.png" alt="image-20200619155754832" style="zoom: 80%;" />



















































