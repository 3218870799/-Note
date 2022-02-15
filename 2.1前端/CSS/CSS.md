- 

2：link与@import的差别

link标签除了可以加载CSS外，还可以做很多其它的事情，比如定义RSS，定义rel连接属性等，@import就只能加载CSS了。

当一个页面被加载的时候（就是被浏览者浏览的时候），link引用的CSS会同时被加载，而@import引用的CSS会等到页面全部被下载完再被加载。所以有时候浏览@import加载CSS的页面时开始会没有样式（就是闪烁），网速慢的时候还挺明显。

兼容性的差别。由于@import是CSS2.1提出的所以老的浏览器不支持，@import只有在IE5以上的才能识别，而link标签无此问题。

使用dom控制样式时的差别。当使用javascript控制dom去改变样式的时候，只能使用link标签，因为@import不是dom可以控制的。

@import可以在css中再次引入其他样式表，比如可以创建一个主样式表，在主样式表中再引入其他的样式表，





网格布局生成器：

https://cssgrid-generator.netlify.app/

https://csslayout.io/



# 选择器

基本选择器

层次选择器

结构选择器

属性选择器



span标签：重点要突出的字，使用span套起来

# 样式

## 字体样式

```css
<!--
字体风格：粗体，斜体，意大利体
font-weight:字体粗细
font-size:字体大小
font-family:字体样式
color:字体颜色
将上边的变成一个
font：字体风格,字体粗细,字体大小,字体样式
-->
body{
    font-family:楷体;
}

```

## 文本样式

```css
<!--
颜色：RGB 0-F
text-align:排版，居中
text-index:2em;段落首行缩进
heiht：300px;块高
line-height:300px;行高
当行高和块高一样就可以上下居中
text-decoration：装饰，a标签去掉下划线直接设置成none就ok
-->
h1{
    color:rgba(1,255,255)
}

```

## 列表

## 盒子模型

![CSS box-model](https://www.runoob.com/images/box-model.gif)

- **Margin(外边距)** - 清除边框外的区域，外边距是透明的。
- **Border(边框)** - 围绕在内边距和内容外的边框。
- **Padding(内边距)** - 清除内容周围的区域，内边距是透明的。
- **Content(内容)** - 盒子的内容，显示文本和图像。

## 圆角边框

```css
div{
    width:100px;
    height:100px;
    border:10px solid red;
    border-radius:100px;
}
```

## 浮动

块级元素：独占一行

h1—h6； p ； div； 列表

行内元素：不独占一行

span；a； img；strong；

调整

```css
display:block(块元素);inline(行内元素);inline-block:(是块元素但是可以在一行)none(消失)
```

float：左右浮动

## 定位

相对定位：

绝对定位

z-index



# 动画

菜鸟拙见，望请指正！

## 原理：

创建动画的原理是，将一套 CSS 样式逐渐变化为另一套样式。在动画过程中，您能够多次改变这套 CSS 样式。以百分比来规定改变发生的时间，或者通过关键词 "from" 和 "to"，等价于 0% 和 100%。0% 是动画的开始时间，100% 动画的结束时间

### 2：@keyframes规则:

```css
@keyframes animationname {/*animationname为动画的名称*/
        keyframes-selector {/*keyftames-selector为动画时长百分比，值为从0~100%*/
              css-styles;
        }
}
```

但是目前浏览器都不支持 @keyframes 规则。Firefox 支持替代的 @-moz-keyframes 规则。Opera 支持替代的 @-o-keyframes 规则。Safari 和 Chrome 支持替代的 @-webkit-keyframes 规则。

###  3：使用animate将动画与元素绑定：

css的animate的具体操作参考文档  http://www.w3school.com.cn/cssref/pr_animation.asp

```css
 div
{
 animation:mymove 5s infinite;/*infinite表示重复无限次*/
 -webkit-animation:mymove 5s infinite; /* Safari 和 Chrome */
}
```

辨析transform

transform的含义是：改变，使变形，转换，

​        属性有**：**`rotate()`（旋转） / `skew()`（倾斜） / `scale()`（比例） / `translate()`（位移）并且还有x，y轴之分

区别在于动画可以循环，而transform只执行一次

## 网站参考

4：W3school里给了这样一个例子:http://www.w3school.com.cn/tiy/t.asp?f=css3_keyframes4,但是这样一个例子仅仅教会怎么用，但如何才能用它做出好看的效果呐？接下来介绍一些动画库，这些动画库做了许多好看的动画，并且大多都有源码，看这些动画库的目的主要还是学习人家怎么做的！不可完全套用

4.1animate.css是来自dropbox的工程师Daniel Eden开发的一款CSS3的动画效果小类库。包含了60多款不同类型的CSS3动画，包括：晃动，闪动，各种淡出淡出效果，如果你想快速的整合各种CSS3动画特效的话，使用它即可方便的实现

查看演示：[https://daneden.github.io/animate.css/ ](https://daneden.github.io/animate.css/)                     

github地址：https://github.com/daneden/animate.css 

 使用时直接将animate.css引入，然后元素类名设为animate + 动画名称即可，或用JQuery为元素添加类名                   

4.2magic.css

查看演示：

http://www.17sucai.com/pins/demoshow/10001

github地址：

https://github.com/miniMAC/magic

使用时直接将magic.css引入，然后元素类名设为magictime + 动画名称即可，或用JQuery为元素添加类名 

4.3Effeckt.css

查看演示：

http://www.gbtags.com/gb/linkviewer/3147.htm

4.4.hover.css 

查看演示：

http://ianlunn.github.io/Hover/

github地址：

[https://github.com/IanLunn/Hover ](https://github.com/IanLunn/Hover)



## JS操作动画：

可以通过动画使样式以一定的速率逐渐变化。如果要实现移动的动画效果，则可以对被设定为position：absolute的元素的left属性进行渐变。如果要实现淡入淡出的效果，则可以逐渐改变元素的透明度opacity的值。

为了使样式可以以一定的速率逐渐变化，自然就需要让javascript定期执行，set Interval能够定期执行javascript函数，css3也可，推荐css3实现

```
 <div id = "foo" style = "position:absolute">This is a sample.</div>
 <script>
    var elem = document.getElementById('foo');
    var frame = 0;
    setInterval(function(){
        frame+=1;
        elem.style.left = frame*10+'px';
    },100);//每经过100毫秒将向右移动10px
 </script>
```

## JQ辅助操作动画

ＪＱｕｅｒｙ不仅提供了一些更改样式的简单方法，还准备了不少能够在执行动画的同时更改样式的方法。这些方法常常能够接受以下参数：ｄｕｒａｔｉｏｎ（动画持续时间），ｅａｓｉｎｇ（动画加速度），以及动画结束时的处理方式（ｃａｌｌｂａｃｋ） 

| 方法    | 说明                            | 方法        | 说明                                   |
| ------- | ------------------------------- | ----------- | -------------------------------------- |
| hide    | 隐藏元素                        | show        | 显示元素                               |
| toggle  | 切换元素的隐藏状态              | fadeln      | 对元素执行淡入效果                     |
| fadeOut | 对元素执行淡出效果              | fadeToggle  | 如果元素被隐藏则淡入，否则淡出         |
| fadeTo  | 将元素不透明度渐变为opacity参数 | slideDown   | 向下滑动元素                           |
| slideUp | 向上滑动元素                    | slideToggle | 如果元素被隐藏则向下滑动，否则向上滑动 |
| animate | 将元素的css变为props的状态      | stop        | 停止动画                               |
| queue   | 取得队列                        | dequeue     | 去除队列中的第一个元素并执行           |
| delay   | 以duration为时间暂停队列的处理  |             |                                        |

### JQuery之animate（）方法自定义动画

7.1 语法

```css
 $(selector).animate({params},speed,callback);
 /*必需的 params 参数定义形成动画的 CSS 属性。*/
 /*可选的 speed 参数规定效果的时长。它可以取以下值："slow"、"fast" 或毫秒。*/
 /*可选的 callback 参数是动画完成后所执行的函数名称。*/
```

7.2 实例:

```html
<!DOCTYPE html>
<html>
<head>
<script src="/jquery/jquery-1.11.1.min.js"></script>
<script> 
$(document).ready(function(){
  $("button").click(function(){
    $("div").animate({
      height:'toggle'
    });
  });
});
</script> 
</head>
 
<body>

<button>开始动画</button>
<p>默认情况下，所有 HTML 元素的位置都是静态的，并且无法移动。如需对位置进行操作，记得首先把元素的 CSS position 属性设置为 relative、fixed 或 absolute。甚至可以把属性的动画值设置为 "show"、"hide" 或 "toggle"：</p>
<div style="background:#98bf21;height:100px;width:100px;position:absolute;">
</div>

</body>
</html> 
```

​    7.3  animate的用法很多，并且默认地，jQuery 提供针对动画的队列功能

   7.4 停止动画stop（） 关闭动画  JQuer.fx.off属性值设为false

# 渐变

最近看到一遍关于渐变的文章，感觉很好，只是收藏感觉太可惜了，就转载了，好好学习！

 

CSS3 Gradient分为linear-gradient(线性渐变)和radial-gradient(径向渐变)。而我们今天主要是针对线性渐变来剖析其具体的用法。为了更好的应用[CSS3 Gradient](http://dev.w3.org/csswg/css3-images/),我们需要先了解一下目前的几种现代浏览器的内核，主流内容主要有[Mozilla](http://www.mozilla.org/)（Gecko）（熟悉的有Firefox，Flock等浏览器）、[WebKit](http://www.webkit.org/)（熟悉的有Safari、Chrome等浏览器）、[Opera](http://www.opera.com/)(presto)（Opera浏览器）、Trident（讨厌的IE浏览器）。本文照常忽略IE不管，我们主要看看在Mozilla、Webkit、Opera下的应用，当然在IE下也可以实现，他需要通过IE特有的滤镜来实现，在后面会列出滤镜的使用语法，但不会具体介绍如何实用，感兴趣的可以搜索相关技术文档。那我们了解了这些，现在就开始今天的主题吧。

**CSS3的线性渐变**

**一、线性渐变在Mozilla下的应用**

**语法：**

```
-moz-linear-gradient( [<point> || <angle>,]? <stop>, <stop> [, <stop>]* )
```

**参数：**其共有三个参数，第一个参数表示线性渐变的方向，top是从上到下、left是从左到右，如果定义成left top，那就是从左上角到右下角。第二个和第三个参数分别是起点颜色和终点颜色。你还可以在它们之间插入更多的参数，表示多种颜色的渐变。如图所示：

![img](http://www.w3cplus.com/sites/default/files/moz-gradient.png)

根据上面的介绍，我们先来看一个简单的例子：

**HTML：**

```
<div class="example example1"></div> 
```

**CSS：**

```
.example {
   width: 150px;
   height: 80px;
 }
```

　　

 

(如无特殊说明，我们后面的示例都是应用这一段html和css 的基本代码)

现在我们给这个div应用一个简单的渐变样式：

```
1 .example1 {
2    background: -moz-linear-gradient( top,#ccc,#000);
3 }
```

效果如下：

![img](http://www.w3cplus.com/sites/default/files/gradient1.png)

注：这个效果暂时只有在Mozilla内核的浏览器下才能正常显示。

**二、线性渐变在Webkit下的应用**

**语法：**

```
-webkit-linear-gradient( [<point> || <angle>,]? <stop>, <stop> [, <stop>]* )//最新发布书写语法
-webkit-gradient(<type>, <point> [, <radius>]?, <point> [, <radius>]? [, <stop>]*) //老式语法书写规则 
```

**参数：**-webkit-gradient是webkit引擎对渐变的实现参数，一共有五个。第一个参数表示渐变类型（type），可以是linear（线性渐变）或者radial（径向渐变）。第二个参数和第三个参数，都是一对值，分别表示渐变起点和终点。这对值可以用坐标形式表示，也可以用关键值表示，比如 left top（左上角）和left bottom（左下角）。第四个和第五个参数，分别是两个color-stop函数。color-stop函数接受两个参数，第一个表示渐变的位置，0为起点，0.5为中点，1为结束点；第二个表示该点的颜色。如图所示：

![img](http://www.w3cplus.com/sites/default/files/webkit-gradient.png)

![img](http://www.w3cplus.com/sites/default/files/wekit-gradient-new.png)

我们先来看一个老式的写法示例：

```
background: -webkit-gradient(linear,center top,center bottom,from(#ccc), to(#000));
```

 

效果如下所示

![img](http://www.w3cplus.com/sites/default/files/gradient1.png)

接着我们在来看一下新式的写法：

```
-webkit-linear-gradient(top,#ccc,#000);
```

 

这个效果我就不在贴出来了，大家在浏览器中一看就明白了，他们是否一致的效果。仔细对比，在Mozilla和Webkit下两者的学法都基本上一致了，只是其前缀的区别，当然哪一天他们能统一成一样，对我们来说当然是更好了，那就不用去处理了。将大大节省我们的开发时间哟。

**三、线性渐变在Opera下的应用**

**语法：**

```
-o-linear-gradient([<point> || <angle>,]? <stop>, <stop> [, <stop>]); /* Opera 11.10+ */
```

 

参数：-o-linear-gradient有三个参数。第一个参数表示线性渐变的方向，top是从上到下、left是从左到右，如果定义成left top，那就是从左上角到右下角。第二个和第三个参数分别是起点颜色和终点颜色。你还可以在它们之间插入更多的参数，表示多种颜色的渐变。（注：Opera支持的版本有限，本例测试都是在Opera11.1版本下，后面不在提示），如图所示：

![img](http://www.w3cplus.com/sites/default/files/opera-gradient.png)

示例：

```
background: -o-linear-gradient(top,#ccc, #000);
```

 

效果如图所示

![img](http://www.w3cplus.com/sites/default/files/gradient1.png)

**四、线性渐变在Trident (IE)下的应用**

**语法：**

```
filter: progid:DXImageTransform.Microsoft.gradient(GradientType=0, startColorstr=#1471da, endColorstr=#1C85FB);/*IE<9>*/
-ms-filter: "progid:DXImageTransform.Microsoft.gradient (GradientType=0, startColorstr=#1471da, endColorstr=#1C85FB)";/*IE8+*/
```

 

IE依靠滤镜实现渐变。startColorstr表示起点的颜色，endColorstr表示终点颜色。GradientType表示渐变类型，0为缺省值，表示垂直渐变，1表示水平渐变。如图所示：

![img](http://www.w3cplus.com/sites/default/files/ie-gradient-filter.png)

上面我们主要介绍了线性渐变在上述四大核心模块下的实现方法，接着我们主要针对线性渐变在MOZ、Webkit、Opera三大模块下实现各种不同线性渐变实例：

从上面的语法中我们可以很清楚的知道，要创建一个线性渐变，我们需要创建一个起点和一个渐变方向（或角度），定义一个起始色：

```
-moz-linear-gradient( [<point> || <angle>,]? <stop>, <stop> [, <stop>]* )
-webkit-linear-gradient( [<point> || <angle>,]? <stop>, <stop> [, <stop>]* )
-o-linear-gradient( [<point> || <angle>,]? <stop>, <stop> [, <stop>]* )
```

 

具体应用如下：

```
background:-moz-linear-gradient(left,#ace,#f96);/*Mozilla*/
background:-webkit-gradient(linear,0 50%,100% 50%,from(#ace),to(#f96));/*Old gradient for webkit*/
background:-webkit-linear-gradient(left,#ace,#f96);/*new gradient for Webkit*/
background:-o-linear-gradient(left,#ace,#f96); /*Opera11*/
```

 

效果如下：

![img](http://www.w3cplus.com/sites/default/files/gradient.png)

起始点（Starting Point）的工作方式类似于background position。您可以设置水平和垂直位置为百分比，或以像素为单位，或在水平方向上可以使用left/center/right，在垂直方向上可以使用top/center/bottom。位置起始于左上角。如果你不指定水平或垂直位置，它将默认为center。其工作方式主要包含：Top → Bottom、Left → Right、bottom → top、right → left等，接着我们主要一种一种来看其实现的效果：

**1、开始于center（水平方向）和top（垂直方向）也就是Top → Bottom：**

```
/* Firefox 3.6+ */
background: -moz-linear-gradient(top, #ace, #f96); 
/* Safari 4-5, Chrome 1-9 */ 
/* -webkit-gradient(,  [, ]?,  [, ]? [, ]*) */
background: -webkit-gradient(linear,top,from(#ace),to(#f96));
/* Safari 5.1+, Chrome 10+ */
background: -webkit-linear-gradient(top, #ace, #f96);
/* Opera 11.10+ */
background: -o-linear-gradient(top, #ace, #f96);
```

 

效果：

![img](http://www.w3cplus.com/sites/default/files/gradient3.png)

**2、始于left（水平方向）和center（垂直方向）也是就Left → Right：**

```
/* Firefox 3.6+ */
background: -moz-linear-gradient(left, #ace, #f96);
/* Safari 5.1+, Chrome 10+ */
background: -webkit-linear-gradient(left, #ace, #f96);
/* Opera 11.10+ */
background: -o-linear-gradient(left, #ace, #f96);
```

 

效果如下：

![img](http://www.w3cplus.com/sites/default/files/gradient4.png)

**3、起始于left（水平方向）和top（垂直方向）:**

```
background: -moz-linear-gradient(left top, #ace, #f96);
background: -webkit-linear-gradient(left top, #ace, #f96);
background: -o-linear-gradient(left top, #ace, #f96);
```

 

效果如下：

![img](http://www.w3cplus.com/sites/default/files/gradient5.png)

**4、Linear Gradient (with Even Stops):**

```
/* Firefox 3.6+ */ 
background: -moz-linear-gradient(left, #ace, #f96, #ace, #f96, #ace); 
/* Safari 4-5, Chrome 1-9 */ 
background: -webkit-gradient(linear, left top, right top, from(#ace), color-stop(0.25, #f96), color-stop(0.5, #ace), color-stop(0.75, #f96), to(#ace)); 
/* Safari 5.1+, Chrome 10+ */ 
background: -webkit-linear-gradient(left, #ace, #f96, #ace, #f96, #ace); 
/* Opera 11.10+ */ 
background: -o-linear-gradient(left, #ace, #f96, #ace, #f96, #ace);
```

 

效果如下：

![img](http://www.w3cplus.com/sites/default/files/gradient6.png)

**5、with Specified Arbitrary Stops：**

```
 /* Firefox 3.6+ */ 
 background: -moz-linear-gradient(left, #ace, #f96 5%, #ace, #f96 95%, #ace); 
 /* Safari 4-5, Chrome 1-9 */ 
 background: -webkit-gradient(linear, left top, right top, from(#ace), color-stop(0.05, #f96), color-stop(0.5, #ace), color-stop(0.95, #f96), to(#ace)); 
 /* Safari 5.1+, Chrome 10+ */ 
 background: -webkit-linear-gradient(left, #ace, #f96 5%, #ace, #f96 95%, #ace); 
 /* Opera 11.10+ */ 
 background: -o-linear-gradient(left, #ace, #f96 5%, #ace, #f96 95%, #ace);
```

 

效果如下：

![img](http://www.w3cplus.com/sites/default/files/gradient7.png)

**6、角度(Angle)：**

正如上面看到的示例，如果您不指定一个角度，它会根据起始位置自动定义。如果你想更多的控制渐变的方向，您不妨设置角度试试。例如，下面的两个渐变具有相同的起点left center，但是加上一个30度的角度。

没有角度的示例代码：

```
background: -moz-linear-gradient(left, #ace, #f96);
background: -webkit-linear-gradient(left,#ace,#f96);
background: -o-linear-gradient(left, #ace, #f96);
```

 

加上30度的角度代码：

```
background: -moz-linear-gradient(left 30deg, #ace, #f96);
background: -webkit-gradient(linear, 0 0, 100% 100%, from(#ace),to(#f96));
background: -o-linear-gradient(30deg, #ace, #f96);
```

 

效果图如下：

![img](http://www.w3cplus.com/sites/default/files/gradient8.png)   ![img](http://www.w3cplus.com/sites/default/files/gradient9.png)

当指定的角度，请记住，它是一个由水平线与渐变线产生的的角度，逆时针方向。因此，使用0deg将产生一个左到右横向梯度，而90度将创建一个从底部到顶部的垂直渐变。我来看看你核心代码：

```
background: -moz-linear-gradient(<angle>, #ace, #f96);
background: -webkit-gradient(<type>,<angle>, from(#ace), to(#f96));
background: -webkit-linear-gradient(<angle>, #ace, #f96);
background: -o-linear-gradient(<angle>, #ace, #f96);
```

 

我们来看看各角度的区别

```
 1 .deg0 {
 2   background: -moz-linear-gradient(0deg, #ace, #f96);
 3   background: -webkit-gradient(linear,0 50%,100% 50%,from(#ace),to(#f96));
 4   background: -webkit-linear-gradient(0deg, #ace, #f96);
 5   background: -o-linear-gradient(0deg, #ace, #f96);
 6 }
 7     
 8 .deg45 {
 9   background: -moz-linear-gradient(45deg, #ace, #f96);
10   background: -webkit-gradient(linear,0 100%,100% 0%,from(#ace),to(#f96));
11   background: -webkit-linear-gradient(45deg, #ace, #f96);
12   background: -o-linear-gradient(45deg, #ace, #f96);
13 }
14 .deg90 {
15   background: -moz-linear-gradient(90deg, #ace, #f96);
16   background: -webkit-gradient(linear,50% 100%,50% 0%,from(#ace),to(#f96));
17   background: -webkit-linear-gradient(90deg, #ace, #f96);
18   background: -o-linear-gradient(90deg, #ace, #f96);
19 }
20 .deg135 {
21   background: -moz-linear-gradient(135deg, #ace, #f96);
22   background: -webkit-gradient(linear,100% 100%,0 0,from(#ace),to(#f96));
23   background: -webkit-linear-gradient(135deg, #ace, #f96);
24   background: -o-linear-gradient(135deg, #ace, #f96);
25 }
26 .deg180 {
27   background: -moz-linear-gradient(180deg, #ace, #f96);
28   background: -webkit-gradient(linear,100% 50%,0 50%,from(#ace),to(#f96));
29   background: -webkit-linear-gradient(180deg, #ace, #f96);
30   background: -o-linear-gradient(180deg, #ace, #f96);
31 }
32 .deg225 {
33   background: -moz-linear-gradient(225deg, #ace, #f96);
34   background: -webkit-gradient(linear,100% 0%,0 100%,from(#ace),to(#f96));
35   background: -webkit-linear-gradient(225deg, #ace, #f96);
36   background: -o-linear-gradient(225deg, #ace, #f96);
37 }
38 .deg270 {
39   background: -moz-linear-gradient(270deg, #ace, #f96);
40   background: -webkit-gradient(linear,50% 0%,50% 100%,from(#ace),to(#f96));
41   background: -webkit-linear-gradient(270deg, #ace, #f96);
42   background: -o-linear-gradient(270deg, #ace, #f96);
43 }
44 .deg315 {
45   background: -moz-linear-gradient(315deg, #ace, #f96);
46   background: -webkit-gradient(linear,0% 0%,100% 100%,from(#ace),to(#f96));
47   background: -webkit-linear-gradient(315deg, #ace, #f96);
48   background: -o-linear-gradient(315deg, #ace, #f96);
49 }
50 .deg360 {
51   background: -moz-linear-gradient(360deg, #ace, #f96);
52   background: -webkit-gradient(linear,0 50%,100% 50%,from(#ace),to(#f96));
53   background: -webkit-linear-gradient(360deg, #ace, #f96);
54   background: -o-linear-gradient(360deg, #ace, #f96);
55 }
```

 

 

效果如下：

![img](http://www.w3cplus.com/sites/default/files/gradient10.png)

除了起始位置和角度，你应该指定起止颜色。起止颜色是沿着渐变线，将会在指定位置（以百分比或长度设定）含有指定颜色的点。色彩的起止数是无限的。如果您使用一个百分比位置，0％代表起点和100％是终点，但区域外的值可以被用来达到预期的效果。 这也是通过CSS3 Gradient制作渐变的一个关键所在，其直接影响了你的设计效果，像我们这里的示例都不是完美的效果，只是为了能给大家展示一个渐变的效果，大家就这样先用着吧。我们接着看一下不同的起址色的示例：

```
background: -moz-linear-gradient(top, #ace, #f96 80%, #f96);
background: -webkit-linear-gradient(top,#ace,#f96 80%,#f96);
background: -o-linear-gradient(top, #ace, #f96 80%, #f96);
```

 

效果如下：

![img](http://www.w3cplus.com/sites/default/files/graduebt11.png)

如果没有指定位置，颜色会均匀分布。如下面的示例：

```
background: -moz-linear-gradient(left, red, #f96, yellow, green, #ace);
background: -webkit-linear-gradient(left,red,#f96,yellow,green,#ace);
background: -o-linear-gradient(left, red, #f96, yellow, green, #ace);
```

 

效果如下

![img](http://www.w3cplus.com/sites/default/files/gradient12.png)

**7、渐变上应用透明─透明度(Transparency)：**

透明度还支持透明渐变。这对于制作一些特殊的效果是相当有用的，例如，当堆叠多个背景时。这里是两个背景的结合：一张图片，一个白色到透明的线性渐变。我们来看一个官网的示例吧：

```
background: -moz-linear-gradient(right, rgba(255,255,255,0), rgba(255,255,255,1)),url(http://demos.hacks.mozilla.org/openweb/resources/images/patterns/flowers-pattern.jpg);
background: -webkit-linear-gradient(right, rgba(255,255,255,0), rgba(255,255,255,1)),url(http://demos.hacks.mozilla.org/openweb/resources/images/patterns/flowers-pattern.jpg);
background: -o-linear-gradient(right, rgba(255,255,255,0), rgba(255,255,255,1)),url(http://demos.hacks.mozilla.org/openweb/resources/images/patterns/flowers-pattern.jpg);
```

 

接着看看效果吧

![img](http://www.w3cplus.com/sites/default/files/gradient13.png)

大家可以时入[这里](http://demos.hacks.mozilla.org/openweb/resources/images/patterns/flowers-pattern.jpg)和原图做一下比较，是不是很神奇呀。如果想体会的话，快点动手跟我一起做吧。

上面我们主要介绍了CSS3中线性渐变，文章一开始说过CSS3渐变包含两个部分，其一就是我们说的线性渐变，其二就是我们接下来要说的径向渐变。

**CSS3的径向渐变**

CSS3的径向渐变和其线性渐变是很相似的。我们首先来看其**语法**：

```
 -moz-radial-gradient([<bg-position> || <angle>,]? [<shape> || <size>,]? <color-stop>, <color-stop>[, <color-stop>]*);
 -webkit-radial-gradient([<bg-position> || <angle>,]? [<shape> || <size>,]? <color-stop>, <color-stop>[, <color-stop>]*);
```

 

**（需要特别说明一点的是，径向渐变到目前还不支持Opera的内核浏览器，所以我们径向渐变都是在firefox,safari,chrome底下进行测试完成的。）**

除了您已经在线性渐变中看到的起始位置，方向，和颜色，径向梯度允许你指定渐变的形状（圆形或椭圆形）和大小（最近端，最近角，最远端，最远角，包含或覆盖 (closest-side, closest-corner, farthest-side, farthest-corner, contain or cover)）。 颜色起止(Color stops)：就像用线性渐变，你应该沿着渐变线定义渐变的起止颜色。下面为了更好的理解其具体的用法，我们主要通过不同的示例来对比CSS3径向渐变的具体用法

**示例一：**

```
 background: -moz-radial-gradient(#ace, #f96, #1E90FF);
 background: -webkit-radial-gradient(#ace, #f96, #1E90FF);
```

 

效果：

![img](http://www.w3cplus.com/sites/default/files/gradient14.png)

**示例二：**

```
 background: -moz-radial-gradient(#ace 5%, #f96 25%, #1E90FF 50%);
 background: -webkit-radial-gradient(#ace 5%, #f96 25%, #1E90FF 50%);
```

 

效果：

![img](http://www.w3cplus.com/sites/default/files/graduient15.png)

从以上俩个示例的代码中发现，他们起止色想同，但就是示例二定位了些数据，为什么会造成这么大的区别呢？其实在径向渐变中虽然具有相同的起止色，但是在没有设置位置时，其默认颜色为均匀间隔，这一点和我们前面的线性渐变是一样的，但是设置了渐变位置就会按照渐变位置去渐变，这就是我们示例一和示例的区别之处：虽然圆具有相同的起止颜色，但在示例一为默认的颜色间隔均匀的渐变，而示例二每种颜色都有特定的位置。

**示例三**

```
  background: -moz-radial-gradient(bottom left, circle, #ace, #f96, #1E90FF);
  background: -webkit-radial-gradient(bottom left, circle, #ace, #f96, #1E90FF);
```

 

效果

![img](http://www.w3cplus.com/sites/default/files/gradient17.png)

**示例四**

```
background: -moz-radial-gradient(bottom left, ellipse, #ace, #f96, #1E90FF);
background: -webkit-radial-gradient(bottom left, ellipse, #ace, #f96, #1E90FF);
```

 

效果

![img](http://www.w3cplus.com/sites/default/files/gradient18.png)

示例三和示例四我们从效果中就可以看出，其形状不一样，示例三程圆形而示例四程椭圆形状，也是就是说他们存在形状上的差异。然而我们在回到两个示例的代码中，显然在示例三中设置其形状为circle而在示例四中ellipse，换而言之在径向渐变中，我们是可以会渐变设置其形状的。

**示例五**

```
background: -moz-radial-gradient(ellipse closest-side, #ace, #f96 10%, #1E90FF 50%, #f96);
background: -webkit-radial-gradient(ellipse closest-side, #ace, #f96 10%, #1E90FF 50%, #f96);
```

 

效果：

![img](http://www.w3cplus.com/sites/default/files/gradient19.png)

**示例六**

```
background: -moz-radial-gradient(ellipse farthest-corner, #ace, #f96 10%, #1E90FF 50%, #f96);
background: -webkit-radial-gradient(ellipse farthest-corner, #ace, #f96 10%, #1E90FF 50%, #f96);
```

 

效果：

![img](http://www.w3cplus.com/sites/default/files/gradient20.png)

从示例五和示例六中的代码中我们可以清楚知道，在示例五中我人应用了closest-side而在示例六中我们应用了farthest-corner。这样我们可以知道在径向渐变中我们还可以为其设置大小(Size)：size的不同选项(closest-side, closest-corner, farthest-side, farthest-corner, contain or cover)指向被用来定义圆或椭圆大小的点。 示例：椭圆的近边VS远角 下面的两个椭圆有不同的大小。示例五是由从起始点(center)到近边的距离设定的，而示例六是由从起始点到远角的的距离决定的。

**示例七：**

```
background: -moz-radial-gradient(circle closest-side, #ace, #f96 10%, #1E90FF 50%, #f96);
background: -webkit-radial-gradient(circle closest-side, #ace, #f96 10%, #1E90FF 50%, #f96);
```

 

效果：

![img](http://www.w3cplus.com/sites/default/files/gradient21.png)

**示例八：**

```
background: -moz-radial-gradient(circle farthest-side, #ace, #f96 10%, #1E90FF 50%, #f96);
background: -webkit-radial-gradient(circle farthest-side, #ace, #f96 10%, #1E90FF 50%, #f96);
```

 

效果：

![img](http://www.w3cplus.com/sites/default/files/gradient22.png)

示例七和示例八主要演示了圆的近边VS远边 ，示例七的圆的渐变大小由起始点(center)到近边的距离决定，而示例八的圆则有起始点到远边的距离决定。

**示例九：**

```
background: -moz-radial-gradient(#ace, #f96, #1E90FF);
background: -webkit-radial-gradient(#ace, #f96, #1E90FF);
```

 

效果：

![img](http://www.w3cplus.com/sites/default/files/gradient23.png)

**示例十：**

```
background: -moz-radial-gradient(contain, #ace, #f96, #1E90FF);
background: -webkit-radial-gradient(contain, #ace, #f96, #1E90FF);
```

 

效果：

![img](http://www.w3cplus.com/sites/default/files/gradient24.png)

示例九和示例十演示了包含圆 。在这里你可以看到示例九的默认圈，同一渐变版本，但是被包含的示例十的圆。

最后我们在来看两个实例一个是应用了中心定位和full sized,如下所示：

```
1  /* Firefox 3.6+ */ 
2  background: -moz-radial-gradient(circle, #ace, #f96); 
3  /* Safari 4-5, Chrome 1-9 */ 
4  /* Can't specify a percentage size? Laaaaaame. */ 
5  background: -webkit-gradient(radial, center center, 0, center center, 460, from(#ace), to(#f96)); 
6  /* Safari 5.1+, Chrome 10+ */ 
7  background: -webkit-radial-gradient(circle, #ace, #f96);  
```

 

 

效果如下：

![img](http://www.w3cplus.com/sites/default/files/graduient25.png)

下面这个实例应用的是Positioned, Sized，请看代码和效果

```
1 /* Firefox 3.6+ */ 
2 /* -moz-radial-gradient( [ || ,]? [ || ,]? , [, ]* ) */
3 background: -moz-radial-gradient(80% 20%, closest-corner, #ace, #f96); 
4 /* Safari 4-5, Chrome 1-9 */
5 background: -webkit-gradient(radial, 80% 20%, 0, 80% 40%, 100, from(#ace), to(#f96)); 
6 /* Safari 5.1+, Chrome 10+ */
7 background: -webkit-radial-gradient(80% 20%, closest-corner, #ace, #f96);
```

 

 

效果：

![img](http://www.w3cplus.com/sites/default/files/gradient26.png)

到此关于CSS3的两种渐变方式我们都介绍完了。在浪费大家一点时间，我们看看CSS的**重复渐变的应用。**

如果您想重复一个渐变，您可以使用-moz-repeating-linear-gradient和-moz-repeating-radial-gradient。 在下面的例子，每个实例都指定了两个起止颜色，并无限重复。

```
1 background: -moz-repeating-radial-gradient(#ace, #ace 5px, #f96 5px, #f96 10px);
2 background: -webkit-repeating-radial-gradient(#ace, #ace 5px, #f96 5px, #f96 10px);
1 background: -moz-repeating-linear-gradient(top left -45deg, #ace, #ace 5px, #f96 5px, #f96 10px);
2 background: -webkit-repeating-linear-gradient(top left -45deg, #ace, #ace 5px, #f96 5px, #f96 10px);
```

效果：

![img](http://www.w3cplus.com/sites/default/files/gradient27.png)     ![img](http://www.w3cplus.com/sites/default/files/gradient28.png)

有关于CSS3渐变的东西就完了，大家看完了肯定会想，他主要用在哪些方面呢？这个说起来就多了，最简单的就是制作背景，我们还可以应用其制作一些漂亮的按钮，还可以用他来制作patterns，我在这里列出几种制作patterns的示例代码吧：

HTML代码：

```
1 <ul>
2    <li class="gradient gradient1"></li>
3    <li class="gradient gradient2"></li>
4    <li class="gradient gradient3"></li>
5    <li class="gradient gradient4"></li>
6    <li class="gradient gradient5"></li>
7    <li class="gradient gradient6"></li>
8    </ul> 
```

CSS 代码：

```
  1 ul {
  2   overflow: hidden;
  3   margin-top: 20px;
  4 }
  5 li{
  6   width: 150px;
  7   height: 80px;
  8   margin-bottom: 10px;
  9   float: left;
 10   margin-right: 5px;
 11   background: #ace;
 12   /*Controls the size*/
 13   -webkit-background-size: 20px 20px;
 14   -moz-background-size: 20px 20px;
 15   background-size: 20px 20px; 
 16 }
 17     
 18 li.gradient1 {
 19   background-image: -webkit-gradient(
 20     linear,
 21     0 100%, 100% 0,
 22     color-stop(.25, rgba(255, 255, 255, .2)), 
 23     color-stop(.25, transparent),
 24     color-stop(.5, transparent), 
 25     color-stop(.5, rgba(255, 255, 255, .2)),
 26     color-stop(.75, rgba(255, 255, 255, .2)), 
 27     color-stop(.75, transparent),
 28     to(transparent)
 29     );
 30   background-image: -moz-linear-gradient(
 31     45deg, 
 32     rgba(255, 255, 255, .2) 25%, 
 33     transparent 25%,
 34     transparent 50%, 
 35     rgba(255, 255, 255, .2) 50%, 
 36     rgba(255, 255, 255, .2) 75%,
 37     transparent 75%, 
 38     transparent
 39     );
 40   background-image: -o-linear-gradient(
 41     45deg, 
 42     rgba(255, 255, 255, .2) 25%, 
 43     transparent 25%,
 44     transparent 50%, 
 45     rgba(255, 255, 255, .2) 50%, 
 46     rgba(255, 255, 255, .2) 75%,
 47     transparent 75%, 
 48     transparent
 49   );
 50   background-image: linear-gradient(
 51     45deg, 
 52     rgba(255, 255, 255, .2) 25%, 
 53     transparent 25%,
 54     transparent 50%, 
 55     gba(255, 255, 255, .2) 50%, 
 56     rgba(255, 255, 255, .2) 75%,
 57     transparent 75%, 
 58     transparent
 59     );
 60 }
 61 
 62 li.gradient2 {
 63    background-image: -webkit-gradient(linear, 0 0, 100% 100%,
 64       color-stop(.25, rgba(255, 255, 255, .2)), color-stop(.25, transparent),
 65       color-stop(.5, transparent), color-stop(.5, rgba(255, 255, 255, .2)),
 66       color-stop(.75, rgba(255, 255, 255, .2)), color-stop(.75, transparent),
 67       to(transparent));
 68    background-image: -moz-linear-gradient(-45deg, rgba(255, 255, 255, .2) 25%, transparent 25%,
 69       transparent 50%, rgba(255, 255, 255, .2) 50%, rgba(255, 255, 255, .2) 75%,
 70       transparent 75%, transparent);
 71    background-image: -o-linear-gradient(-45deg, rgba(255, 255, 255, .2) 25%, transparent 25%,
 72       transparent 50%, rgba(255, 255, 255, .2) 50%, rgba(255, 255, 255, .2) 75%,
 73       transparent 75%, transparent);
 74    background-image: linear-gradient(-45deg, rgba(255, 255, 255, .2) 25%, transparent 25%,
 75       transparent 50%, rgba(255, 255, 255, .2) 50%, rgba(255, 255, 255, .2) 75%,
 76       transparent 75%, transparent);
 77 }
 78     
 79 li.gradient3 {
 80   background-image: -webkit-gradient(linear, 0 0, 0 100%, color-stop(.5, rgba(255, 255, 255, .2)), color-stop(.5, transparent), to(transparent));
 81   background-image: -moz-linear-gradient(rgba(255, 255, 255, .2) 50%, transparent 50%, transparent);
 82   background-image: -o-linear-gradient(rgba(255, 255, 255, .2) 50%, transparent 50%, transparent);
 83   background-image: linear-gradient(rgba(255, 255, 255, .2) 50%, transparent 50%, transparent);
 84 }
 85     
 86 li.gradient4 {
 87   background-image: -webkit-gradient(linear, 0 0, 100% 0, color-stop(.5, rgba(255, 255, 255, .2)), color-stop(.5, transparent), to(transparent));
 88   background-image: -moz-linear-gradient(0deg, rgba(255, 255, 255, .2) 50%, transparent 50%, transparent);
 89   background-image: -o-linear-gradient(0deg, rgba(255, 255, 255, .2) 50%, transparent 50%, transparent);
 90   background-image: linear-gradient(0deg, rgba(255, 255, 255, .2) 50%, transparent 50%, transparent);
 91 }
 92     
 93 li.gradient5 {
 94   background-image: -webkit-gradient(linear, 0 0, 100% 100%, color-stop(.25, #555), color-stop(.25, transparent), to(transparent)),
 95       -webkit-gradient(linear, 0 100%, 100% 0, color-stop(.25, #555), color-stop(.25, transparent), to(transparent)),
 96       -webkit-gradient(linear, 0 0, 100% 100%, color-stop(.75, transparent), color-stop(.75, #555)),
 97       -webkit-gradient(linear, 0 100%, 100% 0, color-stop(.75, transparent), color-stop(.75, #555));
 98   background-image: -moz-linear-gradient(45deg, #555 25%, transparent 25%, transparent),
 99      -moz-linear-gradient(-45deg, #555 25%, transparent 25%, transparent),
100      -moz-linear-gradient(45deg, transparent 75%, #555 75%),
101      -moz-linear-gradient(-45deg, transparent 75%, #555 75%);
102   background-image: -o-linear-gradient(45deg, #555 25%, transparent 25%, transparent),
103      -o-linear-gradient(-45deg, #555 25%, transparent 25%, transparent),
104      -o-linear-gradient(45deg, transparent 75%, #555 75%),
105      -o-linear-gradient(-45deg, transparent 75%, #555 75%);
106   background-image: linear-gradient(45deg, #555 25%, transparent 25%, transparent),
107     linear-gradient(-45deg, #555 25%, transparent 25%, transparent),
108     linear-gradient(45deg, transparent 75%, #555 75%),
109     linear-gradient(-45deg, transparent 75%, #555 75%);
110 }
111     
112 li.gradient6 {
113   background-image: -webkit-gradient(linear, 0 0, 0 100%, color-stop(.5, transparent), color-stop(.5, rgba(200, 0, 0, .5)), to(rgba(200, 0, 0, .5))),
114      -webkit-gradient(linear, 0 0, 100% 0, color-stop(.5, transparent), color-stop(.5, rgba(200, 0, 0, .5)), to(rgba(200, 0, 0, .5)));
115   background-image: -moz-linear-gradient(transparent 50%, rgba(200, 0, 0, .5) 50%, rgba(200, 0, 0, .5)),
116      -moz-linear-gradient(0deg, transparent 50%, rgba(200, 0, 0, .5) 50%, rgba(200, 0, 0, .5));
117   background-image: -o-linear-gradient(transparent 50%, rgba(200, 0, 0, .5) 50%, rgba(200, 0, 0, .5)),
118      -o-linear-gradient(0deg, transparent 50%, rgba(200, 0, 0, .5) 50%, rgba(200, 0, 0, .5));
119   background-image: linear-gradient(transparent 50%, rgba(200, 0, 0, .5) 50%, rgba(200, 0, 0, .5)),
120      linear-gradient(0deg, transparent 50%, rgba(200, 0, 0, .5) 50%, rgba(200, 0, 0, .5));
121 }
```

 效果：

![img](http://www.w3cplus.com/sites/default/files/gradient29.png)

## 继承

屏蔽掉父元素的影响：

方式一：覆盖

默认继承的属性：
