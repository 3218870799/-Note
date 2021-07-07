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

