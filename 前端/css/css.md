#### css注释

/* css内容 */

#### style

可以在html中的head中添加style进行添加样式

需要标签选择或者属性选择进行添加样式，格式是 属性：值;

##### Css的引入方式

- 内联样式

  在html标签上添加style属性实现

- 内部样式

  在 head中的style标签中添加样式

- 引入外部的css样式

  引入一个单独的css文件，

  - link

    在head中添加link标签

    rel 引入资源的类型

    href 引入资源的地址

  - import (不建议使用)

    在head中添加style添加`@import url("资源的地址")`  进行引用

#### 单位

- px 像素，

- % 百分比，相对的大小的，根据父级标签进行百分比

#### css中的颜色

- 单词表示法

- 十六进制表示法

- RGB颜色表示法

  rgb(255,255,255);

- transparent 透明色

#### 基本样式

- width 宽

- height  高

- opacity 设置透明度

- background-color 背景色

- background-image 背景图片

  url(背景地址) 

- background-repeat 背景图片的平铺方式

  repeat-x x平铺

  repeat-y y平铺

  repeat 默认 会水平垂直的都铺满背景图

  no-repeat 都不平铺,只会在容器中显示一个图片

- background-position 背景图片的位置

  数字：

  ​		x y  如果是正数，就向下和向右移动，如果是负数就向上和向左移动。 

  单词：

  ​		right容器最右边

  ​		bottom 容器的最下面

  ​		center 居中

  百分比也可以。

- background-attachment 背景图片随滚动条的移动方式

  scroll: 默认值，背景位置是按照当前元素进行偏移的，随着滚动条移动，背景图页也会移动

  fixed：背景图不会移动，是按照浏览器进行偏移的，会一直保存在浏览器的某个位置，

#### CSS的边框样式

- border-style 边框样式

  solid：实线

  dashed：虚线

  dotted：点线

- border-width 边框大小

- broder-color 边框颜色

- broder-top-color 边框可以根据某一条边进行设置 border-方向-属性:属性值。

#### 文字样式

- font-family 字体类型

  英文字体添加到中文上实没有效果，中文字体可以使英文字体生效。

  后面可以跟多个字体：优先级从前向后，如果电脑里有就显示，没有就向后识别。

  衬线体和非衬线体

  ​	衬线体实有棱角的，非衬线体实圆滑的

- font-size  字体大小

  数字

  ​	默认16px

  ​	字体一般设置成偶数，

  单词 （一般不使用）

  ​	xx-small 最小

  ​	x-small 较小

  ​	small 小

  ​	medium 正常

  ​	large 大

  ​	x-large 较大

  ​	xx-large 最大

- font-weight

  单词

  ​	noraml 正常

  ​	bold 加粗

  数值 （100-900,正白数字）

  ​	100-500 正常

  ​	600-900 加粗

- font-style 字体样式

  正常 noraml

  斜体 italic、oblique

  区别：

  ​	italic，所有带有倾斜字体的才可以设置倾斜操作，

  ​	oblique 没有倾斜属性的字体也可以设置倾斜操作

#### 段落样式

- text-indent:文本首行缩进

  ox单位：一般设置的大写的实根据文字大小来设置，文件大小*2

  em单位：实文字的个数 2em表示2个文字 （一般使用这个）

  如果文件中出现中英文混合会出现对不齐的情况

- text-align 文本对其方式

  left、center、right、justify(两端对其)

- text-decoration 文本装饰

  下划线 underline

  删除线 line-through

  上划线 overline

  不添加任何装饰：none

  可以添加多个文本修饰符

- text-transform 文本大写

  大写：lowercase

  小写：uppercase 

  制针对收字母大写：capitalize 

- line-height 定义行高

  一行文字的高度，由上下边距和文字的大小组成，行与行的上下边距是相等的

  默认行高是随字体大小进行变化的

  数字：10px

  比例值：如n， 它跟文字大小成比例   它的实际值是n*文字大小

  设置line-height 和父级元素的高度一样来达到文字居中的效果

  ```html
  .box {
      width: 100px;
      height: 100px;
      line-height: 100px;
      text-align: center;
      background-color: red;
  }
  <div class="box">哇啦侧</div>
  ```

  

- letter-spacing  字直接的间距

- word-spacing 定义次之间的间距（针对英文）

  如果单词和或者数字特别长不会强制换行，可以使用下面两种样式进行设置

  word-break：break-all（非常强烈的换行）

  word-warp：break-word （不太强烈的折行，如果当前行放下会把整个单词移动到一行而后换行）

#### CSS复合样式

一个css属性只是控制一种样式，叫做单一样式

一个css属性控制多种样式，叫做复合样式

尽量不要混写，如果分要混写，那么一定要先写复合样式再写单一样式，否则样式会被覆盖

- 复合样式写法

  样式的不同属性之前通过空格间隔

  复合写法是不需要关心顺序的例如background和broder，由的需要关系顺序，如font

- 复合样式：

  background:

  ​	background: 背景 背景图片地址 平铺方式  上下的位置   左右位置 

  ​	可以有多个背景图片，逗号隔开，先写的再上面，后写再下面。

  broder：

  ​	broder: 边框大小 边框颜色 边框样式

  font：

  ​	最少需要由两个值,并且关系顺序，且必须写在最后面，其他的属性可以在它前面

  ​	size family 

  ​	weight style size family 

  ​	style  weight size family 

  ​	weight style size/line-height family 	

#### CSS选择器

- ID选择器

  #id名称{样式}

  注意

  id值在一个页面需要唯一 

  命名规范，字母\_\-数字，不能以数字开头

  命名方式：驼峰式，下划线式，短线式

- 标签选择器

  标签名称{样式}

  使用场景

  ​	1、去掉某些标签的默认样式的时候

  ​	2、复杂的选择器中，如层级选择器

- 类选择器

  .类名称{样式}

  html种class的写法 

  ​	`class="box content"`，这个是给标签添加了两个类，一个是box，一个是content

  注意：

  ​	class选择可以复用，多个标签可以设置相同的class的值

  ​	可以添加多个calss样式

  ​	多个样式的时候，是根据css样式的定义优先级根据决定的，而不是根据标签中class属性中的写的顺序

  ​	标签+类的写法

#### 组合选择器

下面的M和N都是选择器，可以是类选择器可以是标签或者ID选择器

- 层次选择器

  标签选择器.类选择器{样式}

  - 后代选择器

    M N{样式}

    ​	ul li{样式}

    ​	#xx li{样式}

    在指定的条件下找到指定的集合

    选择某一个标签内容所有子标签

  - 父子选择器

    M>N{样式}

    只是选择M标签的孩子标签，而不是选择孩子的孩子的标签。

  - 兄弟选择器

    M ~ N{样式}

    当前所有M下面的所有兄弟N标签

  - 相邻选择器

    当前M相邻的下面的N标签，不会找上面的

- 群组选择器

  标签,.class,#id{样式}

  通过逗号给多个不同的选择器添加统一的css样式.

- 属性选择器

  = 完全匹配

  *= 部分匹配

  ^=起始匹配

  $=结束匹配

  `div[class]{样式}` 选择所有带有class的div标签

  `div[class="xxx"]{样式}` 选择所有class是xxx的div标签。

  `div[class*="xxx"]{样式}` 选择所有class是值中带有xxx的div标签

  `div[class="xx"][id]` 选择所有class是值中带有xxx,且带有id的div标签

- 统配选择器

  *{样式}

  给所有的内容都会添加样式

  避免使用统配选择器

- 伪类选择器

  CSS伪类用于向某些元素添加特殊的效果，一般用于初始样式添加不上的时候用伪类来添加

  或者监听指定标签的变化，而后修改某些某些元素的属性的值，如下拉菜单的实现。

  M:伪类{样式}

  - :link 访问前的t添加样式 只能添加给a标签

  - :visited 访后前添加样式 只能添加给a标签

  - :hover 鼠标移入时的样式 可以给所有标签添加

    `#mem:hover ul{display:block} `当鼠标移动到class 包含mem的标签的时候，会给下的ul添加 display=block的属性

    #mem ul li:hover{backgroud:gray} 当鼠标一定到 mem ul 下的li的时候，会给li添加背景颜色。

  - :active 鼠标按下时候的样式 可以给所有的标签添加

  - :after 把给定的文本内容添加到选中的元素后面,或者指定的元素里面，实际上是添加了一个span标签。 

    并且可以添加样式添加各种样式，

    div:after{content:"word",color:red;}

  - :befor 把给定的文本内容添加到选中的元素前面,并且可以添加样式

  - :checked,:disabled :focus {样式} 都是针对表单元素的

    :checked 当选择框被选择的时候会添加的样式 

    :disabled  当选择框被禁用的时候会添加的样式

    :focus 当获取到光标或者焦点的时候会添加的样式 

  - :nth-of-type(数值|n){}

    li:ntd-of-type(3){ background:red } 会直接给li的第三项添加样式，下标从1开始

    li:ntd-of-type(n){ background:red }  n表示0-无穷，这时候所有li都会添加样式

    li:ntd-of-type(2n){ background:red}   表示所有偶数的行，都会添加样式

    li:ntd-of-type(2n){ background:red }  表示所有奇数的行，都会添加样式

    上面可以实现隔行换色的需求

  - :nth-child()

    li:ntd-of-child(2){ background:red } 给ul中第二个li标签设置样式,如果第二个不是li，则会设置失败。

  - :first-of-type

    li:first-of-type{}  li的第一个添加样式

  - :last-of-type

    li:last-of-type{}  li的最后一个添加样式

  - :only-of-type

    li:last-of-type{}  在ul中只包含一个li的时候，给这个li添加样式

     。。。 还有特别多的伪类可以自己去了解

#### CSS的样式继承

标签嵌套的时候，子标签继承父标签

​	文字的样式可以被继承，

​	布局类的样式不能被继承

如果想让子标签继承父标签的样式

​	可以设置具体属性的值为 inherit

​	`p{border:inherit}` p标签会继承其父标签管不border的样式

#### CSS的优先级

​	不同的选择器对同一个元素进行样式设置，当产生相同的样式时，谁的优先级高，以谁的优先级作为最终的选择结果。

- 当设置同样的样式时，后面的优先级比前面的优先级高

- 内部和外部优先相同，谁后引用，谁的优先级高。

- 单一选择器的优先级

  style直接设置到标签>id选择器设置的>class选择器设置的>tag选择器设置的>*选择器设置的>继承的

  style直接设置到标签 权重1000

  id选择器 100

  class选择器 10

  tag 选择器 1

- 标签+类的选择器优先级大于单类的优先级

- 群组优先级和单一选择器的优先级相同。

- 层级优先级

  - 权重比较

    ul li .box p input {}

    .hello span #elem{}

    比较这种优先级可以把他们的权重相加，而后比较

  - 约分比较

    把相同等级的去掉，而后比较剩下的选择器的优先级

- 优先级提升important

  不能针对继承的优先级提升，直接作用与标签的时候有效

  表示提升这个优先级至最高

  #elem{ colore:red !important}

#### CSS的盒子模型

组成由内到外：

*  content->padding->border->margin

  - content  物品

    内容区域由宽和高组成

  - border  包装盒

    边框，不会占用我们的内容区域

  - margin  盒子与盒子直接的间距

    外填充，外边距，边框外面的距离，一般设置两个盒子之间的距离

    和padding类似，值可以是多个也可以是一个

  - padding  包装盒和物品之间的填充物

    内边距，内填充，也就是边框和内容区域之间的间隔

    值的个数：	

    ​	一个值：上下左右

    ​	两个值：上下  左右

    ​	四个值：上 右 下 左

    单一样式只能写一个值

    ​	padding-方向(left,right,top,bottom)  进行某个方向的设置

  背景颜色会填充到margin以内的区域，也就是content、padding、border的颜色是一样的，但是border的颜色可以额外设置，

  文字会在content区域

  padding不能出现负值，margin是可以出现负值的，margin出现负数之后会向反反向偏移。

- box-sizing

  盒子尺寸，可以改变盒子模型的展示形态

  正常情况下box-sizing：content-box ; 设置的宽高是不包含padding,border，只是content宽高，

  当我们改变盒模型之后，box-sizing: border-box; 设置width height 会包含padding,border,是他们3个之和 

  使用场景：

  ​	不用计算，设置的是整个盒子的大小。border和content的值会自己计算好

  ​	解决百分比的位置，我们设置宽度等于 百分比 是整个盒子的，padding是值是在百分几之内计算的。

  ```html
  <!DOCTYPE html>
  <html lang="en">
  
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Document</title>
      <style>
          input {
              width: 100%;
              padding: 10px;
              // 如果我们设置下面这个，则设置宽度为 100%,在设置padding, 会在浏览器下面出现滚动条
              // 因为100%只是 content内容的100%，padding的值还需要额外计算，
              // 当我设置了下面，则设置了100%。则计算padding 是在100%之内进行计算。
              box-sizing: border-box;
          }
      </style>
  </head>
  
  <body>
      <input type="text" name="" id="">
  </body>
  
  </html>
  ```

- margin自适应居中

  可以设置盒子居中显示，自适应就是使用全部可以使用的距离大小，

  只有左右自适应可以居中，上下不可以。

  ```html
  <!DOCTYPE html>
  <html lang="en">
  
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Document</title>
      <style>
          #box1 {
              background-color: red;
              height: 100px;
              width: 400px;
              /* margin-left: auto;
              margin-right: auto; */
              /* 一般会这样写 */
              margin: 0 auto;
          }
  
          #box2 {
              background-color: blue;
              height: 100px;
              width: 100px;
              /* margin-top: 100px; */
          }
      </style>
  </head>
  
  <body>
      <div id="box1">
      </div>
  
  </body>
  
  </html>
  ```

- 不设置content的现象

  width、height不设置的时候对盒子的影响，

  如何子容器不设置宽度的时候会把父容器直接撑满，一般我们撑满的时候不用写`width:100%`，会自动根据盒模型计算出其他的值，如果设置的的时候，这时候再去设置padding,border等一些属性，则子容器会超出父容器。

- 盒子模型的问题

  - margin 叠加

    当给两个盒子同时添加上下外边距的时候，就会出现叠加的问题，这个问题是有上下有，左右时没有的。

    累加的结果时谁的值大，按照谁的来。

    问题解决：

    ​	一般会只是给一个元素添加边距。

    ```html
    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
        <style>
            #box1 {
                background-color: red;
                height: 200px;
                width: 200px;
                margin-bottom: 20px;
            }
    
            #box2 {
                background-color: blue;
                height: 200px;
                width: 200px;
                margin-top: 40px;
            }
        </style>
    </head>
    
    <body>
        // 下面这个两个盒子之间的上下间距时40px,而不是60px
        <div id="box1"></div>
        <div id="box2"></div>
    </body>
    
    </html>
    ```

  - margin-top的 传递

    margin传递的问题只是出现在嵌套的结构中，且只有margin-top会传递的问题，

    嵌套的div或者ul内部嵌套div，如果我们给内部的div设置margin-top,则外部的div或者url也会向下偏移，

    解决：

    1. 给外部的div添加边框可以解决

    2. 把margin换成padding去实现目标，天之padding时候因为，盒子会自动扩展，所以我们可以改变盒子的高度

       ```html
       <!DOCTYPE html>
       <html lang="en">
       
       <head>
           <meta charset="UTF-8">
           <meta name="viewport" content="width=device-width, initial-scale=1.0">
           <title>Document</title>
           <style>
               #box1 {
                   background-color: red;
                   height: 100px;
                   width: 200px;
                   padding-top: 100px;
               }
       
               #box2 {
                   background-color: blue;
                   height: 100px;
                   width: 100px;
                   /* margin-top: 100px; */
               }
           </style>
       </head>
       
       <body>
           <div id="box1">
               <div id="box2"></div>
           </div>
       
       </body>
       
       </html>
       ```

    3. 给外部的元素设置属性

       overflow: hidden

#### 标签分类

- block 块

  div、p、ul、li、h1 ...

  独占一行

  支持所有样式

  不写宽度的时候和父容器的宽度一样

- inline 内联 行内元素

  span a em  img

  挨在一行的

  有些样式不支持，例如 width height margin padding

  不行宽度的时候，宽度有内容决定，如span标签修饰文字的时候，由文字的像素决定

  内联标签之间会有空袭，是由于我们的在写内联标签的时候代码换行产生的，也可以通过其他方式及结局

  ​	在内联标签的父容器上下设置`font-size:0;`，而后在内联标签上重新添加文字样式即可

- inline-block 

  input select ...

  挨在一次，支持所有的样式。

> 布局一般用块标签，修饰文本一般用内联标签，

#### 修改标签类型

可以修改标签的标签类型，display的属性

inline

block

inline-block

None  隐藏标签，不在页面中显示，不占用空间的隐藏

> visibility:hidden 是占用空间的隐藏。

#### 标签嵌套规范

ul li

dl dt dd 

table tr td

块标签可以嵌套内联标签，

块标签不一定能够嵌套块标签，如p 标签中不能够嵌套div标签。

内联标签一般不能嵌套块标签。 如a标签中可以嵌套div标签，给某个区域添加连接

#### overflow 

内容溢出元素框会发生什么情况

- `visible`-默认。溢出不会被裁剪。内容在元素框外渲染
- `hidden` -溢出被裁剪，其余内容将不可见
- `scroll` -剪辑了溢出，并添加了滚动条以查看其余内容
- `auto`-与相似`scroll`，但仅在必要时添加滚动条

#### CSS的float浮动

文档流：文档中可显示对象在文档的排列时所占用的位置

块在文档中默认展示方式就是从上到下，内联标签的文档中的展示方式模式时从左到右

加浮动的元素会脱离文档流，不占原来的空间，占用的是浮动之后的空间，会沿着父容器靠左，或者靠右的排列，如果之前已经有浮动的元素，会挨着浮动的元素进行排列,所以浮动元素可能叠加在原来已有标签的上面。

浮动的元素不会影响父容器的大小了，已经脱离正常文档流。

- float的取值：

  left 浮动的元素靠父容器的左边排列

  right浮动的元素靠父容器的右边排列

  none(默认)

- float的注意点

  - 只会影响后面的元素，浮动起来的元素，不会影响它前面的元素，

    ```html
    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
        <style>
            body {
                border: 1px black solid;
            }
    
            #box1 {
                height: 100px;
                width: 100px;
                background-color: orange;
            }
    
            #box2 {
                height: 200px;
                width: 200px;
                background-color: blue;
                /*  
                这个float后 box2 还是在box1下面，box3会跑到box2的位置
                不会影响浮动元素之前的元素，之后影响之后的元素
                */
                float: left;
            }
    
            #box3 {
                height: 300px;
                width: 300px;
                background-color: black;
            }
        </style>
    </head>
    
    <body>
        <div id="box1"></div>
        <div id="box2"></div>
        <!-- box2不会只写覆盖box3中的文字，文字会被挤开 -->
        <div id="box3">嘻嘻嘻嘻嘻嘻嘻</div>
    </body>
    
    </html>
    ```

  - 内容默认提升半层

    如上：box2不会只写覆盖box3中的文字，文字会被挤开，这样可以使用图片混排的页面

  - 默认宽根据内容决定

    添加的浮动的元素的宽度，不会由父容器的宽度决定而是由自身内容的宽度决定。

  - 换行排列

  - 主要给块元素添加，但是也可以给内联元素添加。

- 清除浮动

  有时候我们不希望我们的元素对下面的元素由影响，这时候可以给不想受影响的元素添加清除浮动的属性。

  他们之间还是处于不同的层级，只是该元素清除了浮动的影响。

  - 上下排序 清除浮动

    clear属性，表示清除浮动，left(清除左浮动)、right(清除右浮动)、both(左右都清除)，

    clear 只会操作块标签。对内联标签不起作用。

    ```html
    <!DOCTYPE html>
    <html lang="en">
    
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
        <style>
            .div1 {
                width: 200px;
                height: 200px;
                background-color: red;
                float: right;
            }
    
            .div2 {
                width: 100px;
                height: 100px;
                background-color: blue;
                margin-top: 60px;
                /* 如果没有添加这个责div2会向上移动 */
                clear: both;
            }
        </style>
    </head>
    
    <body>
        </div>
        <div class="div2"></div>
        <div class="div1">
    </body>
    
    </html>
    ```

  - 嵌套层级 清除浮动

    如果div2 嵌套在div1里面，如果div1没有设定固定高，则div1是由div2撑起来的，如果div2设置浮动之后则div1的样式会发生变化，解决方案中有如下几种

    1. 固定宽高

       我们可以div1设置固定宽高，不推荐；这样做不到自适应，如果div2的宽高有修改，div1也需要修改/

    2. 父元素浮动

       父元素浮动后，由于父元素浮动后和资源不属于一个层级，所以也可以解决，但是不推荐。

       会影响到父元素后面的元素的排列

    3. overflow

       给父标签设置  overflow:hidden 如果div2的内容溢出后，则溢出的内容会被截掉

    4. dispaly 

       给父标签设置  dispaly: inline-block 不推荐，父容器模型改变后，会影响到后面的元素。

    5. 设置空标签

       在div1中再添加一个空标签,这个空标签放到div2的下面，而后给这个标签清除浮动上下浮动

       这时候会在多添加一个标签。

    6. after伪类

       推荐：是空标签的加强版。

       ```html
       <!DOCTYPE html>
       <html lang="en">
       
       <head>
           <meta charset="UTF-8">
           <meta name="viewport" content="width=device-width, initial-scale=1.0">
           <title>Document</title>
           <style>
               #box1 {
                   border: 2px black solid;
               }
       
               #box2 {
                   height: 200px;
                   width: 200px;
                   background-color: red;
                   float: left;
               }
       
               .clear::after {
                   content: "";
                   display: block;
                   clear: both;
               }
           </style>
       </head>
       
       <body>
           <div id="box1" class="clear">
               <div id="box2"></div>
           </div>
       </body>
       
       </html>
       ```

#### position 定位

浮动适合左左右布局，定位适合左叠加的布局，再一个元素上面添加另外一个元素。

css的position属性用于指定一个元素再文档的中的定位方式，top,right,bottom,left属性则决定了该元素的最终位置。

- 取值：

  static(默认) 没有任何定位

  relative 相对定位

  absolut 决定定位

  fixed 固定定位

  sticky 粘性定位

  上下的几个都可以 left,top,right,bottom，只是 00 位置不同

- relative相对定位(相对自己的左上点)

  如果没有定位偏移量，对元素本身没有任何影响，不使元素脱离文档流，

  不影响其他元素布局，只会影响自己的位置，不会影响别的元素。

  left,top,right,bottom   是相对与当前元素的自身进行偏移，0 0 点是匀速的左上点。所以向上是bottom,向下是top,向左是right，向右是left,他们的值可以去负值，表示向相反的方向偏移。

- absolute绝对定位

  使元素脱离原有的文档流，也就是变成了浮动元素。

  如果祖先元素或者父元素定义的position的值为relative，fixed sticky ，子元素则偏移使相对于祖先元素发生的偏移，如果没有祖先元素都没有定义position属性，则是偏移是相对于这个文档发生偏移，

  居中对齐

  ```html
  <div style="position: absolute;left: 0; top: 0; right: 0; bottom: 0;margin:0 auto">
              sdasdadasdsssssssssssssss
  </div>
  ```

  

- fixed 固定定位

  是元素脱离原有的文档流，也就是变成了浮动元素。

  是内联元素支持宽高，（让内联元素具备块的特征）

  使块元素默认宽根据内容决定，（让块元素具备内联的特征）

  相对于这个浏览器窗口进行偏移，不受浏览器滚动条的影响。

- sticky 粘性定位

  在指定的位置，进行粘性操作，做一些吸附的功能，

  如果没有定位到某个位置的时候，会保持移动，如果到达了某个位置则会固定到哪个位置。不会发生移动

#### z-index

当两个标签有重叠的时候，可以设置哪个标签在上面哪个标签在下面。

div1 中嵌套div2  div3和div1 同级别

则有z-index比较的时候会先比较 同一级别的，如果同一级别的相等或者没写的时候，才会和子元素的z-index比较

值越大越靠上面

#### 添加圆角

border-radius：20px

border-radius：50% ，圆形

border-radius：左上 右上 右下 左下

#### CSS Sprite

css雪碧也叫css精灵，是一种网页图片应用处理方式，它允许你将一个页面涉及到的所有零星图片，都包含到一张大的图片中去加载。

可以减少图片的质量，网页的图片加载速度块

减少图片的请求的次数，加快网页的打开速度。

#### css默认样式

没有默认样式的：div span

有默认样式的：

​	body marign：8px

​	h1 marign 上下的21.440px font-weight: bold

​	p 也有marign 上下的16px

 	ul li marign 上下的16px padding 左40px

​		默认点：list-style:disc

​	a text-decoration: underline;

​	....

#### css的重置样式

`*{margin:0;padding:0;}` 因为大部分默认样式都是 marign和padding

`ul{list-style:none;}` 取消ul前面默认的点

`a{text-decoration: none;color:#666;}`

`img{dispaly:block}` 

​	问题的现象：图片和文字底部有空隙，内联元素的对齐方式是按照文字基线对象，而不是文字底线对齐。

​	解决方式：

​		`vertical-align: baseline` 这个就是基线对齐方式，也是默认值，可以修改成bottom是底线对齐

​		`img{dispaly:block}` 

写具体页面的时候或一个布局效果的时候：

1. 写结构
2. css重置样式
3. 写具体的样式

#### BFC

具有BFC特征的元素可以看作是隔离了的独立容器，容器里面的元素不会在布局上影响到外面的元素，具有普通容器所没有的一些特性

触发BFC规范的元素，可以形成一个独立的容器，不受外界的影响。从而解决一些布局的问题。

触发BFC的样式

​	浮动：float 出none以外的值

​	绝对定位元素：position(absolute,fixed)

​	dispaly为inline-block,table-cells,flex

​	overflow 除了visible 以外的值，hidden,auto,scroll

##### 解决margin-bottom和margin-top叠加问题

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .div1 {
            width: 100px;
            height: 100px;
            background-color: red;
            margin-bottom: 30px;
        }

        .div2 {
            width: 100px;
            height: 100px;
            background-color: blue;
            margin-top: 60px;
        }
        // class 是box的容器触发了bfc规范，会形成一个独立的容器。不收其他容器的影响。
        .box {
            overflow: hidden;
        }
    </style>
</head>

<body>
    <div class="box">
        <div class="div1"></div>
    </div>
    <div class="box">
        <div class="div2"></div>    
    </div>
</body>

</html>
```

##### 解决margin-top传递的问题，

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .div1 {
            width: 200px;
            height: 200px;
            background-color: red;
            /* 添加上 overflow: hidden 会触发bfc规范则div就是一个独立的容器，不会受其他容器的影响*/
            overflow: hidden;
        }

        .div2 {
            width: 100px;
            height: 100px;
            background-color: blue;
            margin-top: 60px;
        }
    </style>
</head>

<body>
    <div class="div1">
        <div class="div2"></div>
    </div>
</body>

</html>
```

##### 嵌套层级清楚浮动

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .div2 {
            height: 200px;
            width: 200px;
            background-color: red;
            float: left;
        }

        .div1 {
            border: 2px black solid;
            
            overflow: hidden;
        }
    </style>
</head>

<body>
    <div class="div1">
        <div class="div2"></div>
    </div>
</body>

</html>
```

##### 清除浮动，文字受浮动的影响

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .div1 {
            height: 100px;
            width: 100px;
            background-color: red;
            float: left;
        }

        /*  当div1浮动后，div2会上下div1会跑到div2中，且使div1中的文字收到影响，我们可以使用bfc来清楚影响 */
        .div2 {
            height: 200px;
            width: 200px;
            background-color: blue;
            /* 使div2触发bfc规范，而后成为一个独立的容器 */
            overflow: hidden;
        }
    </style>
</head>

<body>
    <div class="div1">

    </div>
    <div class="div2">
        sdas
    </div>
</body>

</html>
```

## css3

#### transition 过渡

样式的过渡效果

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        div {
            width: 100px;
            height: 100px;
            background-color: red;
            /* 规定应用过渡的 CSS 属性的名称。 */
            transition-property: width;
            /* 定义过渡效果花费的时间。 */
            transition-duration: 3s;
            /*
             过渡的延迟时间，也就是鼠标放上去2s之后 
             也可以提前，也就是说延迟总共需要3s，但是提前了2s,也就是说我们能看到的时间是1s
            */
            transition-delay: 2s; 
            /* 
            过渡效果的时间曲线  默认是ease 
            linear 匀速
            ease 逐渐慢下来
            ease-in 加速
            ease-out 减速
            ease-in-out 先加速在减速
            */
            transition-timing-function:ease;
            /* 符合样式 过渡时间，过渡之前的延迟时间 过渡曲线 */
            transition: 1s 2s linear;
        }

        div:hover {
            width: 200px;
            height: 200px;
            background-color: blue;
        }
    </style>
</head>

<body>
    <div></div>
</body>

</html>
```

#### transform-origin

​	设置变形操作的基点。默认是以元素的中心为基点。

​	https://developer.mozilla.org/zh-CN/docs/Web/CSS/transform-origin

#### transform 变形

改变表情的形状

z轴是垂直于电脑的屏幕的

##### translate 位移

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .div1 {
            height: 300px;
            width: 300px;
            border: 1px black solid;
            margin: 30px auto;
        }

        .div2 {
            background-color: red;
            height: 100px;
            width: 100px;
            transition: 2s;
        }

        .div1:hover .div2 {
            /*  移动x和y轴 */
            transform: translate(100px, 100px);
            /* 单独写x或者y轴 */
            /* transform: translateX(100px) */
            /* transform: translateY(100px) */
        }
    </style>
</head>

<body>
    <div class="div1">
        <div class="div2">
        </div>
    </div>
</body>

</html>
```

##### scale(x,y) 缩放 

​	值是比例值，正常大小就是1,会以当前元素中心点进行缩放。宽高的缩放比例一样的时候可以只写一个，-1表示图片的反转。

​	x 是缩放宽 -1 表示x轴反转

​	y 是缩放高 -1 表示y轴反转。

​	单独设置

​		scaleX()

​		scaleY()

##### rotate 旋转 

​	旋转的值 单位是角度deg

​	单独设置
​		rotateX
​		rotateY
​		totateZ和rotate 是等价关系。正值按照顺时针旋转，负值按照逆时针旋转。

##### skew(x,y) 倾斜

​	倾斜的值 单位是角度deg ，正值表示向右，负值表示向左。

x 是x轴的斜切值

y 是y轴的斜切值

##### Transform的注意事项

- 变形操作不会影响到其他元素

-  变形操作只能给添加给块元素，不能添加给内联元素

- 复合写法，同时可以添加多个变形操作，多个操作之间按照空格隔离

  执行顺序，先执行后面的操作，在执行前面的操作。

  translate 会受到rotate scale skew 的影响。所以translate 一般写在第一位，可以设置动画多度查询效果

##### 练习导航

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        * {
            margin: 0;
            padding: 0;
        }

        ul {
            list-style: none;
        }

        ul {
            width: 430px;
            margin: 30px auto;
            /*  超出的部分隐藏 */
            overflow: hidden;
        }

        ul li {
            float: left;
            width: 100px;
            background-color: red;
            color: white;
            line-height: 30px;
            text-align: center;
            margin: 0 5px;
            transform: skewX(-30deg);
            /*  移动到li标签上显时小手 */
            cursor: pointer;

        }

        ul li.active {
            background-color: blue;
        }

        /* transform只能变成块元素 */
        ul li span {
            transform: skewX(30deg);
            display: block;
        }

        ul li:first-child {
            padding-left: 10px;
            margin-left: -10px;
        }

        ul li:last-child {
            padding-right: 20px;
            margin-right: -20px;
        }
    </style>
</head>

<body>
    <div>
        <ul>
            <li class="active"><span>首页</span></li>
            <li><span>关于我们</span></li>
            <li><span>联系我们</span></li>
            <li><span>招聘信息</span></li>
        </ul>
    </div>
</body>

</html>
```

##### 图片旋转

perspective 景深，观察元素的角度。  设置元素被查看位置的视图：也就是垂直于电脑屏幕的z轴的那个位置

transform-style: preserve-3d; 生成一个3d空间

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        * {
            margin: 0;
            padding: 0;
        }

        ul {
            list-style: none;
        }

        ul li img {
            display: block;
        }

        .parent {
            width: 600px;
            height: 300px;
            border: 1px black solid;
            margin: 30px auto;
            perspective: 700px;
        }

        .parent ul {
            width: 128px;
            height: 94px;
            margin: 100px auto;
            position: relative;
            /*  设置使用3d样式 */
            transform-style: preserve-3d;
            transition: 2s;
        }

        .parent ul li {
            width: 100%;
            height: 100%;
            position: absolute;
            left: 0;
            right: 0;
        }

        .parent:hover ul {
            transform: rotateY(360deg);
        }

        .parent ul li:nth-child(1) {
            transform: rotateY(0) translateZ(200px);
        }

        .parent ul li:nth-child(2) {
            transform: rotateY(60deg) translateZ(200px);
        }

        .parent ul li:nth-child(3) {
            transform: rotateY(120deg) translateZ(200px);
        }

        .parent ul li:nth-child(4) {
            transform: rotateY(180deg) translateZ(200px);
        }

        .parent ul li:nth-child(5) {
            transform: rotateY(240deg) translateZ(200px);
        }

        .parent ul li:nth-child(6) {
            transform: rotateY(300deg) translateZ(200px);
        }
    </style>
</head>

<body>
    <div class="parent">
        <ul>
            <li><img src="./img2/1.jpg" alt=""></li>
            <li><img src="./img2/2.jpg" alt=""></li>
            <li><img src="./img2/3.jpg" alt=""></li>
            <li><img src="./img2/4.jpg" alt=""></li>
            <li><img src="./img2/5.jpg" alt=""></li>
            <li><img src="./img2/6.jpg" alt=""></li>
        </ul>
    </div>
</body>

</html>
```

##### 图片旋转

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<style>
    * {
        margin: 0;
        padding: 0;
    }

    img {
        display: block;
    }

    .parent {
        width: 640px;
        height: 320px;
        margin: 30px auto;
        position: relative;
        perspective: 600px;

    }

    .parent div {
        width: 100%;
        height: 100%;
        position: absolute;
        left: 0;
        right: 0;
        /* 图片背面是否隐藏 */
        backface-visibility: hidden;
        transition: 2s;
    }

    .parent div:first-child {
        transform: rotateY(0);
    }

    .parent div:last-child {
        transform: rotateY(-180deg);
    }

    .parent:hover div:first-child {
        transform: rotateY(180deg);
    }

    .parent:hover div:last-child {
        transform: rotateY(0deg);
    }
</style>

<body>
    <div class="parent">
        <div>
            <img src="./img2/img1.jpg" alt="">
        </div>
        <div>
            <img src="./img2/img2.jpg" alt="">
        </div>
    </div>
</body>

</html>
```

#### 扩展背景样式

backgroud-size 背景图的尺寸大小

​	可以设置具体数值。backgroud-size(width,height)

​	cover 覆盖 会把背景图等比放大，覆盖住整个容器，把超出的部分隐藏
​	contain 包含 会把背景图等比放大，会把背景图包含在容器内，会留有空隙

backgroud-origin 背景图的填充位置

​	padding-box 默认 默认是从padding的位置开始填充
​	border-box  从border位置开始填充
​	content-box  从内容区域开始填充

backgroud-clip 背景图的裁剪方式 平铺的才会看出效果

​	padding-box padding区域为背景的最外层区域，会在这个区域进行裁剪背景图，之外的区域没有背景图
​	border-box 默认  border区域为背景的最外层区域，会在这个区域进行裁剪背景图,之外的区域没有背景图
​	content-box  content区域为背景的最外层区域，会在这个区域进行裁剪背景图，之外的区域没有背景图

复合样式的时候，第一个是填充，第二个裁剪

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .div1 {
            height: 1500px;
            width: 1000px;
            margin: 150px auto;
            /* background 背景图地支  平铺类型 背景图的填充位置 背景图的裁剪方式  */
            background: url("./img2/1.jpg") cover content-box content-box;
        }
    </style>
</head>

<body>
    <div class="div1">
    </div>
</body>

</html>
```

#### CSS渐变

[https://developer.mozilla.org/en-US/docs/Web/CSS/linear-gradient#:~:text=Jump%20to%20section,special%20kind%20of%20.](https://developer.mozilla.org/en-US/docs/Web/CSS/linear-gradient#:~:text=Jump to section,special kind of .)

linear-gradient 和radial-gradient  是值,需要添加到background-image属性上

linear-gradient 线性渐变

​	线性渐变 --> linear-gradient 

radial-gradient 径向渐变

​	从中心点到四周

练习：

​	图片上一道白光划过

​	一个容器内添加多个颜色。
​	进度条的实现 [http://wsyks.github.io/2017/02/16/CSS3%E5%AE%9E%E7%8E%B0%E8%BF%9B%E5%BA%A6%E6%9D%A1%E7%9A%84%E5%87%A0%E4%B8%AA%E5%A7%BF%E5%8A%BF/](http://wsyks.github.io/2017/02/16/CSS3实现进度条的几个姿势/)

#### animation 动画

#### 字体图标

文字前面的图标，他不是图片，而是我们自定义的web字体

font-face 是一个css3中的一个模块，他主要是把自己定义的web字体嵌入到你的网页中

好处

- 可以非常方便的改变大小和颜色，

  font-size 直接可以修改

- 放大后不失真

- 减少请求的次数

- 简化网页布局

- 可以使用计算机没有提供的字体

图标库

​	www.iconfont.cn

##### 原理 

​	一般不会这样使用

​	@font-face语法

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
    @font-face{
       	// 定义名称
        font-family:hello;
        // 引用图标库，其中woff等是做兼容不用系统用的。
        src:url(https://at.alicdn.com/t/font_1401963178_8135476.eot);
        src:url(https://at.alicdn.com/t/font_1401963178_8135476.eot?#iefix) format('embedded-opentype'),url(https://at.alicdn.com/t/font_1401963178_8135476.woff) format('woff'),url(https://at.alicdn.com/t/font_1401963178_8135476.ttf) format('truetype'),url(https://at.alicdn.com/t/font_1401963178_8135476.svg#iconfont) format('svg')
    }

    div{
        // 需要在使用的图标库的标签引用定义font-face的名称
        font-family: hello;
    }
    // 设置图标大小和颜色
    span{
        font-family: hello;
        font-size:30px;
        color:red;
    }
    // 通过伪类的方式添加
    .gouwuche:after{ content:"󰅈";}
    </style>
</head>
<body>
    <div>󰅈</div>
    <span>㑈</span>
    <div class="gouwuche"></div>
</body>
</html>
```

##### 阿里图标库的使用

- 从 www.iconfont.cn 里面找到自己需要的图标库文件

- 下载图标库

- 解压下载的文件，把内容复制我们的项目中，demo.html中有图标的使用方式

- 在我们的html文件中引入iconfont.css和iconfont.js是彩色的图标需要的文件

- 在html中添加标签，

  如`<span iconfont iconnDollar></span>` 

  iconfont 是必须添加的iconnDollar是图标的名称

  iconfont.css 中会自动通过伪类把图标添加到标签中

##### 自定义图标

需要用到 icomoon.io 则个网站

- 需要会svg的操作

- 在网站上生成
- 下载解压文件
- 把里面的style.css和font文件复制到我们的项目，style.css会引用font中的内容。

#### 文字阴影

text-shadow 文字阴影

​	text-shadow(x y blur color,x y blur color,...)

​	x 阴影相对于源文字x轴的偏移距离

​	y 阴影相对于源文字y轴的偏移距离

​	blur 模糊值，数值越大越模糊

​	color 阴影的颜色 默认阴影的颜色和文件颜色相同

逗号进行分割可以设置多个阴影

##### 盒子阴影

box-shadow(x y blur spread color inset ,...)

spread 表示沿着阴影的中心点会想外扩散，阴影的范围会变大

color 阴影的颜色 默认阴影的颜色是黑色

inset 内阴影，默认是外阴影，如果设置outset 则阴影会不去作用。

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        ul {
            list-style: none;
        }

        li {
            float: left;
            margin: 50px;
            cursor: pointer;
            position: relative;
            top: 0;
            transition: .5s;
        }

        li:hover {
            top: -3px;
            box-shadow: 0px 5px 10px #cccccc;
        }
    </style>
</head>

<body>
    <ul>
        <li>
            <img src="./img/mi_1.png" alt="">
        </li>
        <li>
            <img src="./img/mi_2.png" alt="">

        </li>
    </ul>
</body>

</html>
```

#### mask 遮罩

mask: url(“图片地址”) 平铺方法 x y /  width height，url(“图片地址”) 平铺方法 x y /  width height，....  ;

​	url 表示遮罩层图片地址，图片只能是png图片

​    平铺方式,默认是xy 平铺。

   x y 表示遮罩层的位置；center center 表示居中

​	width height 表示遮罩图片的宽高 

可以设置多个遮罩

##### box-reflect 倒影

box-reflect: 方向 间距 url("路径")|渐变

​	方向

​		right  右

​		left 左

​		below 下

​		above 上

​	间距

​		倒影和真是图片之间的间距

​	url("路径")|渐变

​		倒影的内容默认是div中的内容，可以通过url给倒影设置遮罩层，路径里面的图片值只能是png图片

​		渐变只是支持透明度的渐变，不支持颜色的渐变

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .box1 {
            width: 298px;
            margin: 30px auto;
            -webkit-box-reflect: below 0px linear-gradient(rgba(255, 255, 255, 0) 50%, rgba(255, 255, 255, 1));
        }
    </style>
</head>

<body>
    <div class="box1">
        <img src="./img/mi_1.png" alt="">
    </div>
</body>

</html>
```

####　模糊 blur 可以针对图片设置模糊值.

```html
img {
	filter: blur(20px);
}
```

#### Calc 

​	可以进行百分几和像素之间的运算

```html
<style>       
    .par {
        width: 400px;
        border: 2px black solid;
        height: 300px;
    }

    .ch {
        width: calc(100% - 100px);
        background-color: red;
        height: 200px;
    }
</style>
<body>
    <div class="box1">
        <img src="./img/mi_1.png" alt="">
    </div>
    <div class="par">
        <div class="ch"></div>
    </div>
</body>
```

#### 分栏布局

colum-count 分栏的个数

colum-width 分栏的宽度

colum-gap 分栏的间距

colum-rule 分栏的边线

colum-span 合并分栏 all  表示合并所以分栏

colum-count和colum-width 不要一起设置

#### 伪元素

伪元素本质上创建了一个有内容的虚拟容器，这个容器不包含任何DOM元素，但是可以包含内容，另外，开发者还可以为伪元素定制样式

如下是选中div中的内容的时候，回给选中的内容添加背景色和字体颜色。而不是给div添加颜色，也就是给选中的内容添加了虚拟容器并添加了样式。

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        div {
            width: 100px;
            height: 200px;
        }

        div::selection {
            background-color: red;
            color: yellow;
        }
    </style>
</head>
<body>
    <div>
        伪元素本质上创建了一个有内容的虚拟容器，这个容器不包含任何DOM元素，但是可以包含内容，另外，开发者还可以为伪元素定制样式
    </div>
</body>
</html> 
```

#### CSS hack

Css Hack 用来解决浏览器的兼容性问题，不同的浏览器编写不同的CSS效果

Hack分类

- css属性前缀

  在css属性前缀添加一些只有特定浏览器可以识别的hack前缀，以达到预期的效果

- 选择器前缀法

  操作样式的时候在选择器前面添加标识。

- IE选择注释法

#### IE低版本场景bug

#### 渐进增强，优雅降级

渐进增强：针对低浏览器的构建页面，而后针对高浏览器做交互达到好的用户的体验

优雅降级：一开始构建完整功能，对低浏览器进行兼容

## Felx

#### Flex弹性盒子 父容器属性

**在父容器上添加dispaly为flex,表示这个容器为弹性盒子，子容器上不用添加。**

##### dispaly

flex 更改父容器为弹性盒子模型。

##### flex-direction 	

需要添加父容器上

控制子项整体布局方向的。是从右向左，还是从左向右 从上向下，或者从下向上

​	row默认，显示为行，方向为当前文档流水平方向，默认情况下是从左向右

​	row-reverse 显示为行，但是方向和row属性值是反的，也就是起时方向从右开始。

​	column 显示为列

​	column-reverse 显示为列，但方向和column-reverse 属性值是反的，起时方向从下开始。

##### flex-warp 

需要添加父容器上

用来控制 子项整体单行显示还是多行显示

nowrap 默认值，表示单行显示，不换行，会自适应，存放所有子元素为一行

warp 宽度不足时换行

warp-reverse 宽度不足时换行，但是是从下往上开始，

##### flex-flow

需要添加父容器上

flex-flow 是flex-direction和flex-warp的缩写，表示flex布局的flow流动特征

第一个值表示方向，第二值表示换行，中间用空格隔开。

##### justify-content

需要添加父容器上

justify-content属性决定了子项元素在主轴方向上的对齐和分布方式，也就是flex-direction 设置的值为主轴，默认为水平方向。

flex-start 表示起始位置对齐

flex-end 表示结束位置对齐

center 表示居中对齐

space-between 表示为两端对齐，between是中间的意思，意思是多余的留空白。间距只在元素中间区域分配。

space-around around是环绕的意思，每个flex子项两侧都是等宽的空白间距，边缘两侧空白距离只有中间空白宽度的一半。

space-evenly evenly均匀、平等的意思，flex子项之间的空白间距完全相等

如果父元素中内容特表多，flex-warp  设置了换行显示，则行于行之间的空隙比较大，是因为，默认纵轴方向是space-evenly 对齐的

##### align-items

align-items中的items指的是flex子项们，因此align-items指的就是flex子项们，相对于flex容器在侧轴纵轴方向的对齐方式。

也是子项们在一行中的对齐方式。

stretch  默认值为 flex子项拉伸，如果子项没有高度，会拉伸为这个父容器的高度

flex-start 表现为容器顶部对齐 ，这时候如果子项没有设置高度，会自适应的子项中内容的高度，并且以父容器顶部为对齐方式

flex-end 表现为容器底部对齐 这时候如果子项没有设置高度，会自适应的子项中内容的高度，并且以父容器底部为对齐方式

center  表现为垂直居中对齐 这时候如果子项没有设置高度，会自适应的子项中内容的高度，并且以父容器中心点为对齐方式

##### align-content

aligen-content可以看成和justify-content是相似且对立的属性，如果所有flex子项只有一行，则align-content属性是没有任何效果的。

也就是flex子项有多行的情况下，多行之间的对起方式

flex-start 表示起始位置对齐

flex-end 表示结束位置对齐

center 表示居中对齐

space-between 表示为两端对齐，between是中间的意思，意思是多余的留空白。间距只在元素中间区域分配。

space-around around是环绕的意思，每个flex子项两侧都是等宽的空白间距，边缘两侧空白距离只有中间空白宽度的一半。

space-evenly evenly

#### Flex弹性盒子 子容器属性

##### order

​	通过设置order改变某一个flex子项的排序位置，所有子项的默认order属性值为0，指越大，越

##### flex-grow

​	扩展flex子项占据剩余空白空间，扩展所侵占的空间就是除去元素外的剩余的空白间隙，

​	默认为0，表示不侵占  如果子元素侵占设置为 1表示全部侵占的，小于1表示侵占的剩余空间的百分比

##### flex-shrink

​	flex-shrink 主要是处理当前flex容器空间不足时候，单个元素的收缩比例，默认为1，0,表示不收缩， 

##### flex-basis

​	定义元素的默认大小。  auto表示默认。

##### flex

​	flex 属性是flex-grow,flex-shrink flex-basis 的缩写

​	如果只有一个值表示 glex-grow 

##### align-self

​	align-self 指控制单独某一个flex子项的垂直对齐方式

​	center 居中

​	flex-start

​	flex-end 

#### Flex 案例

##### 元素居中的实现

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        /* 实现方式1 */
        /* .box1 {
            width: 300px;
            height: 300px;
            border: 1px black solid;
            margin: 0 auto;
            display: flex;
            justify-content: center;
        }

        .box1 .box2 {
            width: 100px;
            height: 100px;
            background-color: red;
            align-self: center;
        } */
        /* 实现方式2 */
        .box1 {
            width: 300px;
            height: 300px;
            border: 1px black solid;
            margin: 0 auto;
            display: flex;
        }

        .box1 .box2 {
            width: 100px;
            height: 100px;
            background-color: red;
            margin: auto;
        }
    </style>
</head>

<body>
    <div class="box1">
        <div class="box2"></div>
    </div>
</body>

</html>
```

##### 三列布局

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        * {
            margin: 0;
            padding: 0;
        }

        .main {
            display: flex;
        }

        #left {
            width: 200px;
            height: 200px;
            background-color: red;
        }

        #center {
            flex: 1;
            height: 300px;
            background-color: yellow;
        }

        #right {
            width: 150px;
            height: 200px;
            background-color: blue;
        }
    </style>
</head>

<body>
    <div class="main">
        <div id="left"></div>
        <div id="center"></div>
        <div id="right"></div>
    </div>
</body>

</html>
```

##### 骰子的点数

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .box1 {
            width: 100px;
            height: 100px;
            border: 1px black solid;
            border-radius: 5px;
            display: flex;
            justify-content: center;
            align-items: center
        }

        .box1 div {
            width: 30%;
            height: 30%;
            background-color: red;
            border-radius: 50%;
        }

        .box2 {
            width: 100px;
            height: 100px;
            border: 1px black solid;
            border-radius: 5px;
            display: flex;
            justify-content: space-between
        }

        .box2 div {
            width: 30%;
            height: 30%;
            background-color: red;
            border-radius: 50%;
        }

        .box2 div:last-child {
            width: 30%;
            height: 30%;
            background-color: red;
            border-radius: 50%;
            align-self: flex-end;
        }

        .box3 {
            width: 100px;
            height: 100px;
            border: 1px black solid;
            border-radius: 5px;
            display: flex;
            justify-content: space-between
        }

        .box3 div {
            width: 30%;
            height: 30%;
            background-color: red;
            border-radius: 50%;
        }

        .box3 div:last-child {
            width: 30%;
            height: 30%;
            background-color: red;
            border-radius: 50%;
            align-self: flex-end;
        }

        .box3 div:nth-child(2) {
            width: 30%;
            height: 30%;
            background-color: red;
            border-radius: 50%;
            align-self: center;
        }

        .box4 {
            width: 100px;
            height: 100px;
            border: 1px black solid;
            border-radius: 5px;
            display: flex;
            /* align-items: center */
        }

        .box4 div {
            width: 100%;
            display: flex;
            flex-wrap: wrap;
            /* align-content: center; */
            justify-content: center;
        }

        .box4 i:nth-child(1) {
            display: block;
            width: 60%;
            height: 30%;
            background-color: red;
            border-radius: 50%;
            align-self: center;
        }

        .box4 i:nth-child(2) {
            display: block;
            width: 60%;
            height: 30%;
            background-color: red;
            align-self: center;
            border-radius: 50%;
        }

        .box5 {
            width: 100px;
            height: 100px;
            border: 1px black solid;
            border-radius: 5px;
            display: flex;
            /* align-items: center */
        }

        .box5 div {
            width: 100%;
            display: flex;
            flex-wrap: wrap;
            /* align-content: center; */
            justify-content: center;
        }

        .box5 i:nth-child(1) {
            display: block;
            width: 90%;
            height: 30%;
            background-color: red;
            border-radius: 50%;
            align-self: center;
        }

        .box5 i:nth-child(2) {
            display: block;
            width: 90%;
            height: 30%;
            background-color: red;
            align-self: center;
            border-radius: 50%;
        }

        .box6 {
            width: 100px;
            height: 100px;
            border: 1px black solid;
            border-radius: 5px;
            display: flex;
            justify-content: space-between
        }


        .box6 div {
            width: 100%;
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
        }

        .box6 i {
            display: block;
            width: 60%;
            height: 30%;
            background-color: red;
            border-radius: 50%;
            align-self: center;
        }
    </style>
</head>

<body>
    <div class="box1">
        <div></div>
    </div>
    <div class="box2">
        <div></div>
        <div></div>
    </div>
    <div class="box3">
        <div></div>
        <div></div>
        <div></div>
    </div>
    <div class="box4">
        <div>
            <i></i>
            <i></i>
        </div>
        <div>
            <i></i>
            <i></i>
        </div>
    </div>
    <div class="box5">
        <div>
            <i></i>
            <i></i>
        </div>
        <div>
            <i></i>
        </div>
        <div>
            <i></i>
            <i></i>
        </div>
    </div>
    <div class="box6">
        <div>
            <i></i>
            <i></i>
            <i></i>
        </div>

        <div>
            <i></i>
            <i></i>
            <i></i>
        </div>
    </div>
</body>

</html>
```



## 布局

#### 等高布局

原理，因为背景色也会在padding区域添加

margin-bottom 是把当前区域向下移动-2000px

padding-bottom 是设置内边距是2000px

他们的设置都是相当于父元素的底部而言，也就说上面两个设置之后又回到了原来的区域。

当向div1中添加元素的时候，由于div2设置margin-bottom和padding-bottom所以他也会显时撑开

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .per {
            border: 10px black solid;
            overflow: hidden;
        }

        .per .div1 {
            width: 100px;
            background-color: red;
            float: left;
            margin-bottom: -2000px;
            padding-bottom: 2000px;
        }

        .per .div2 {
            margin-bottom: -2000px;
            padding-bottom: 2000px;
            width: 100px;
            background-color: blue;
            float: right;
        }
    </style>
</head>

<body>
    // div1 div2的背景色会随着里面内容的增加而增加，不会出现一遍高，一遍地情况
    <div class="per">
        <div class="div1">
            <p>dasasd</p>
        </div>
        <div class="div2"></div>
    </div>
</body>

</html>
```

#### 三列布局

左右固定，中间自适应,

实现方式

- 浮动 使用margin-left 负值来实现的。
- BFC方式，左边固定，右边固定，不影响中间自适应的方式
- 定位，左边定位，右边定位中间用margin居中
- flex弹性

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        * {
            margin: 0;
            padding: 0;
        }

        .header {
            width: 100%;
            height: 200px;
            background-color: red;
        }

        .container .center {
            height: 200px;
            float: left;
            width: 100%;
        }

        .container .center p {
            background-color: yellow;
            /* height: 100px; */
            margin: 0 150px 0 100px;

        }

        .container .left {
            background-color: blue;
            float: left;
            /* margin-left: -100%; */
            height: 200px;
            width: 100px;
            margin-left: -100%;
        }

        .container .right {
            background-color: green;
            float: left;
            height: 200px;
            width: 150px;
            z-index: 10;
            margin-left: -150px;
        }
    </style>
</head>

<body>
    <div class="header"></div>
    <div class="container">
        <div class="center">
            <p>sssss</p>
        </div>
        <div class="left"></div>
        <div class="right"></div>
    </div>
</body>

</html>
```

#### 圣杯布局

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        * {
            margin: 0;
            padding: 0;
        }

        .header {
            width: 100%;
            height: 200px;
            background-color: red;
        }

        .container {
            height: 200px;
            background-color: yellow;
            margin: 0 150px 0 100px;
        }

        .container .center {
            height: 100%;
            float: left;
            width: 100%;
        }

        .container .left {
            background-color: blue;
            float: left;
            height: 200px;
            width: 100px;
            margin-left: -100%;
            position: relative;
            left: -100px;
        }

        .container .right {
            background-color: green;
            float: left;
            height: 200px;
            width: 150px;
            z-index: 10;
            margin-left: -150px;
            position: relative;
            right: -150px;
        }
    </style>
</head>

<body>
    <div class="header"></div>
    <div class="container">
        <div class="center">
            内容区域
        </div>
        <div class="left"></div>
        <div class="right"></div>
    </div>
</body>

</html>
```

#### Grid网格布局

##### Grid作用于父标签的属性

- dispaly:grid 表示把当前父标签形成网格

  通过设置 grid-template-columns和grid-template-rows设置网格的行和列

  会把父容器的孩子容器平均分配到网格中。

- grid-template-columns 

  对网格进行横纵化分的单位可以使百分比，auto，fr(网格剩余空间比例)等单位

  对列进行划分

  grid-template-columns:100px 100px 200px 100px  

  ​	设置网格为4列，第一列列宽，100px，一次向后

  grid-template-columns: 1fr 1fr 1fr; == grid-template-columns: repeat(3,1fr); 

  ​	设置网格4列，每列的列宽为1/3的父元素的宽度。 	

- grid-template-rows

  对行进行划分 

  grid-template-rows:100px auto 25%

  设置网格的行为3行，第一行高100px ,第三行为父元素高的25%，第二行高自动 也就是剩余的部分。 

- grid-template-ares

  grid-template-ares 就是我们的网格划分区域，此时grid子项只要使用grid-area属性指定其隶属于那个区域。

  ```html
  <!DOCTYPE html>
  <html lang="en">
  
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Document</title>
      <style>
          .box2 {
              width: 300px;
              height: 300px;
              border: 1px gray dotted;
              display: grid;
              grid-template-columns: repeat(3, 1fr);
              grid-template-rows: repeat(3, 1fr);
              grid-template-areas:
                  "a1 a1 a1"
                  "a2 a2 a3"
                  "a2 a2 a3";
          }
  
          .box2 div {
              background-color: red;
              border: 1px black solid;
          }
  
          .box2 div:nth-child(1) {
              grid-area: a1;
          }
  
          .box2 div:nth-child(2) {
              grid-area: a2;
          }
  
          .box2 div:nth-child(3) {
              grid-area: a3;
          }
      </style>
  </head>
  
  <body>
      <div class="box2">
          <div></div>
          <div></div>
          <div></div>
      </div>
  </body>
  
  </html>
  ```

- grid-tepmlate:是 grid-template-columns 、grid-template-rows、grid-template-ares符合写法

- grid-column-gap

- grid-row-gap

  grid-column-gap 和grid-row-gap 是定义网格之间的间隙,单位是像素

- grid-gap

  grid-gap 是grid-column-gap 和grid-row-gap 的缩写

  grid-gap:列间距 行间距

- justify-items 

    网格内部的元素在网格里面的水平呈现方式

  ​	stretch 默认的，拉伸，表现为水平或者垂直填充

  ​	start 表现为容器左侧或者顶部对齐

  ​	end 表现为容器右侧或者底部对齐

  ​	center 表示居中对齐

- align-items

   网格内部的元素在网格里面的垂直呈现方式

- place-items

  place-items 是justify-items 和align-items的符合写法

  place-items: 纵向 水平;

- justify-content

  指定的网格元素的水平分布方式

- align-content

  指定的网格元素在父容器中的呈现方式，

  ​	stretch 默认的，拉伸，表现为水平或者垂直填充

  ​	start 表现为容器左侧或者顶部对齐

  ​	end 表现为容器右侧或者底部对齐

  ​	center 表示居中对齐

  ​	space-between 两端对齐

  ​	space-around 

  ​	space-evenly 平均分配空白空间

- place-content

  是justify-content和align-content的符合写法

  place-contet:纵向 水平;

##### Grid作用于子标签的属性

- grid-column-start: 

- grid-column-end: 

- grid-row-start: 

- grid-row-end: 

  grid-column-start，grid-column-end grid-row-start  grid-row-end  的值是数字

  如一个9*9的网格中

  ​	 从外边的线开始数分别是1 2 3 4

  ​	 从最上面开始数是 1 2 3 4

  ![image-20200621231419531](.\images\css\image-20200621231419531.png)

  示例代码

  ```html
  <!DOCTYPE html>
  <html lang="en">
  
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Document</title>
      <style>
          .box1 {
              height: 300px;
              width: 300px;
              border: 1px gray dotted;
              display: grid;
              grid-template-columns: repeat(3, 1fr);
              grid-template-rows: repeat(3, 1fr);
          }
  
          .box1 div {
              background-color: red;
              border: 1px black dotted;
              grid-column-start: 2;
              grid-column-end: 3;
              grid-row-start: 2;
              grid-row-end: 3;
          }
      </style>
  </head>
  
  <body>
      <div class="box1">
          <div></div>
      </div>
  </body>
  
  </html>
  ```

  显示的结果

  ![image-20200621231419531](.\images\css\image-20200621231442086.png)

- grid-column 

  grid-column 是grid-column-start和grid-column-end的简写

  grid-column: 开始 / 结束;

- grid-row 

  grid-row 是grid-row-start，grid-row-end的简写

  grid-row: 开始 / 结束;

- grid-area 

  当前网格所占用的区域，名字和位置的表示方法

  grid-are 3/2/4/4; 第一个值是水平起时位置，第二个值是垂直起始位置，第三个值是水平结束位置，第四个值是垂直结束位置

- justify-self 单个网格元素相对于父容器的的水平对齐方式 

- align-self 单个网格元素相对于父容器的的垂直对齐方式

  ​	stretch 默认的，拉伸，表现为水平或者垂直填充

  ​	start 表现为容器左侧或者顶部对齐

  ​	end 表现为容器右侧或者底部对齐

  ​	center 表示居中对齐

- place-self  是justify-self 和align-self的符合写法

#### Bootstrap

bootstrap-reboot.css 是重置样式的css

bootstrap-grid.css 是用过弹性布局实现的栅格系统

bootstrap.css 包含了bootstrap-reboot.css 和bootstrap-grid.css 

