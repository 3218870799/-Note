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

