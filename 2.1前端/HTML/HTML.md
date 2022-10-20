```html
横线
<hr>
```

表格

```html
<table>
    <!--标题-->
    <caption></caption>
    <!--行标题-->
    <th></th>
    <!--行-->
    <tr>
        <!--列-->
        <td></td>
    </tr>
    <!--表头-->
    <thead></thead>
    <!--表主体-->
    <tboay></tboay>
    <!--表尾-->
    <tfoot></tfoot>
</table>
```

隐藏属性：

注意不要设置display属性，会冲突；

```html
<div hidden> </div>
document.getElementById(‘show').hidden = true;//隐藏该元素
document.getElementById('hidden').hidden = false;//显示该元素
```

独立的网页内容区域

```js
<article></article>
```



事件流三阶段：

1：捕获阶段（从根节点开始顺着目标节点构建一条事件路径，即时间由页面元素接受，逐级向下，到具体元素）

2：目标阶段（到达目标节点，即元素本身）

3：冒泡阶段（从目标节点顺着捕获阶段构建的路径回去，即跟捕获相反具体元素本身逐级向上，到页面元素

不过经常有防止冒泡和捕获：

- w3c的方法是`event.stopPropagation()`，IE则是使用`event.cancelBubble = true`事件处理过程中，阻止了事件冒泡，但不会阻止默认行为

取消默认行为：
