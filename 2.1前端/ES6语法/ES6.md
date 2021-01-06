https://www.runoob.com/w3cnote/es6-symbol.html

# 1：简介

 ECMAScript 6.0 ，是 JavaScript 的下一个版本标准，2015.06 发版。每一年发一版

Node.js支持最高

查看Node对ES6的支持

```cmd
node --v8-options | grep harmony
```

webpack

是一个现代 JavaScript 应用程序的静态模块打包器 (module bundler) 。当 webpack 处理应用程序时，它会递归地构建一个依赖关系图 (dependency graph) ，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle 。

ebpack 主要有四个核心概念:

- 入口 (entry)
- 输出 (output)
- loader
- 插件 (plugins)

# 2：基本语法

## let关键字

不允许重复声明

块儿级作用域：全局，函数，eval

​	let只在块内有效

不存在变量提升

不影响作用域链

let声明的变量只在 let 命令所在的代码块内有效。let 只能声明一次 var 可以声明多次:for 循环计数器很适合用 let

## const关键字

声明必须赋初始值

标识符一般为大写

不允许重复声明

值不允许修改 

块儿级作用域

const 声明一个只读的常量，一旦声明，常量的值就不能改变。

 const 其实保证的不是变量的值不变，而是保证变量指向的内存地址所保存的数据不允许改动。此时，你可能已经想到，简单类型和复合类型保存值的方式是不同的。是的，对于简单类型（数值 number、字符串 string 、布尔值 boolean）,值就保存在变量指向的那个内存地址，因此 const 声明的简单类型变量等同于常量。而复杂类型（对象 object，数组 array，函数 function），变量指向的内存地址其实是保存了一个指向实际数据的指针，所以 const 只能保证指针是固定的，至于指针指向的数据结构变不变就无法控制了，所以使用 const 声明复杂类型对象时要慎重。



## 变量解构赋值

6 允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称 为解构赋值。

```javascript
//对象的解构赋值
const lin = {
 name: '林志颖',
 tags: ['车手', '歌手', '小旋风', '演员']
};
let {name, tags} = lin;
```

## 模板字符串

模板字符串（template string）是增强版的字符串，用反引号（`）标识，特点： 1) 字符串中可以出现换行符 2) 可以使用 ${xxx} 形式输出变量

```javascript
// 定义字符串
let str = `<ul>
			<li>沈腾</li>
 			<li>玛丽</li>
 			<li>魏翔</li>
 			<li>艾伦</li>
		 </ul>`;
// 变量拼接
let star = '王宁';
let result = `${star}在前几年离开了开心麻花`;

```

## 简化对象写法

S6 允许在大括号里面，直接写入变量和函数，作为对象的属性和方法。这样的书写更加简洁。

```javascript
let name = '111';
let slogon = '222';
let improve = function () {
 console.log('可以提高你的技能');
}
//属性和方法简写
let person = {
 name,
 slogon,
 improve,
 //声明函数的简化
 change() {
 	console.log('可以改变你')
 }
};
```

## 箭头函数

ES6 允许使用「箭头」（=>）定义函数。

```javascript
/**
* 1. 通用写法
*/
let fn = (arg1, arg2, arg3) => {
 return arg1 + arg2 + arg3;
}
```

1) 如果形参只有一个，则小括号可以省略 

2) 函数体如果只有一条语句，则花括号可以省略，函数的返回值为该条语句的 执行结果 

3) 箭头函数 this 指向声明时所在作用域下 this 的值 

4) 箭头函数不能作为构造函数实例化 

5) 不能使用 arguments



## 函数参数赋值初始值

1：形参初始值

```javascript

//当不传入第三个参数时，该值就是初始值
function add(a,b,c=10){
    return a + b + c;
}
let result = add(1,2)'
console.log(result);
```

2：与解构赋值结合

```javascript
function connect({options}){
    
}
```

![image-20210105220703862](media/image-20210105220703862.png)



## rest参数

引入 rest 参数，用于获取函数的实参，用来代替 arguments

```javascript
/**
* 作用与 arguments 类似
*/
function add(...args){
 console.log(args);
}
add(1,2,3,4,5);
/**
* rest 参数必须是最后一个形参
*/
function minus(a,b,...args){
 console.log(a,b,args);
}
minus(100,1,2,3,4,5,19);
```

## spread 扩展运算符

扩展运算符（spread）也是三个点（...）。它好比 rest 参数的逆运算，将一个数组转为用逗号分隔的参数序列，对数组进行解包。

```javascript
/**
* 展开数组
*/
let tfboys = ['德玛西亚之力','德玛西亚之翼','德玛西亚皇子'];
let tfboys2 = ['德玛西亚之力2','德玛西亚之翼2','德玛西亚皇子2'];
function fn(){
 console.log(arguments);
}
fn(...tfboys);
let tfboysAll = [...tfboys,tfboys2];
```

## Symbol

引入了一种新的原始数据类型 Symbol ，表示独一无二的值，最大的用法是用来定义对象的唯一属性名。 数据类型除了 Number 、 String 、 Boolean 、 Objec t、 null 和 undefined ，还新增了 Symbol 。

Symbol.for() 类似单例模式，首先会在全局搜索被登记的 Symbol 中是否有该字符串参数作为名称的 Symbol 值，如果有即返回该 Symbol 值，若没有则新建并返回一个以该字符串参数为名称的 Symbol 值，并登记在全局环境中供搜索。

```js
let yellow = Symbol("Yellow");
let yellow1 = Symbol.for("Yellow");
yellow === yellow1;      // false
 
let yellow2 = Symbol.for("Yellow");
yellow1 === yellow2;     // true
```

Symbol.keyFor()

Symbol.keyFor() 返回一个已登记的 Symbol 类型值的 key ，用来检测该字符串参数作为名称的 Symbol 值是否已被登记。

```js
let yellow1 = Symbol.for("Yellow");
Symbol.keyFor(yellow1);    // "Yellow"
```

