---
title: "JavaScript"
date: 2024-10-21T21:56:25.152169
categories: ['Docs']
author: "Ronan"
---
# 对象概述

什么是对象？对象（object）是 JavaScript 语言的核心概念，也是最重要的数据类型

简单说，对象就是一组“键值对”（key-value）的集合，是一种无序的复合数据集合

![img](https://imgs.ronan.us.kg/js1.png)

```js
var user = {
  name: 'itbaizhan',
  age: '13'
};
```

对象的每一个键名又称为“属性”（property），它的“键

值”可以是任何数据类型。如果一个属性的值为函数，通常把这个属性称为“方法”，它可以像函数那样调用

```js
var user = {
  getName: function (name) {
    return name;
  }
};
user.getName("itbaizhan") // itbaizhan
```

如果属性的值还是一个对象，就形成了链式引用

```js
var user = {
    name:"itbaizhan",
    age:13,
    container:{
        frontEnd:["Web前端","Android","iOS"],
        backEnd:["Java","Python"]
    }
}
user.container.frontEnd // ["Web前端","Android","iOS"]
```

# Math对象

Math是 JavaScript 的原生对象，提供各种数****学功能。

### Math.abs()

**`Math.abs`方法返回参数值的绝对值**

```js
Math.abs(1) // 1
Math.abs(-1) // 1
```

### Math.max()，Math.min()

`Math.max`方法返回参数之中最大的那个值，`Math.min`返回最小的那个值。如果参数为空, `Math.min`返回`Infinity`, `Math.max`返回`-Infinity`。

```js
Math.max(2, -1, 5) // 5
Math.min(2, -1, 5) // -1
Math.min() // Infinity
Math.max() // -Infinity
```

### Math.floor()，Math.ceil()

`Math.floor`方法返回小于参数值的最大整数

```js
Math.floor(3.2) // 3
Math.floor(-3.2) // -4
```

`Math.ceil`方法返回大于参数值的最小整数

```js
Math.ceil(3.2) // 4
Math.ceil(-3.2) // -3
```

### Math.random()

`Math.random()`返回0到1之间的一个伪随机数，可能等于0，但是一定小于1

```js
Math.random() // 0.28525367438365223
```

任意范围的随机数生成函数如下

```js
function getRandomArbitrary(min, max) {
  return Math.random() * (max - min) + min;
}

getRandomArbitrary(5, 10)
```

**实时效果反馈**

**1. 下列代码获得一个正整数，横线处应该填写的内容是：**

```js
function ToInteger(x) {
    x = Number(x);
    return ___(___(x));
}
ToInteger(-10.4); // 向下取整：10
```

# Date对象

`Date`对象是 JavaScript 原生的时间库。它以1970年1月1日00:00:00作为时间的零点，可以表示的时间范围是前后各1亿天（单位为毫秒）

### Date.now()

`Date.now`方法返回当前时间距离时间零点（1970年1月1日 00:00:00 UTC）的毫秒数，相当于 Unix 时间戳乘以1000

```js
Date.now();   // 1635216733395
```

### 时间戳

时间戳是指格林威治时间1970年01月01日00时00分00秒(北京时间1970年01月01日08时00分00秒)起至现在的总秒数。

格林威治和北京时间就是时区的不同

Unix是20世纪70年代初出现的一个操作系统，Unix认为1970年1月1日0点是时间纪元。JavaScript也就遵循了这一约束

`Date`对象提供了一系列`get*`方法，用来获取实例对象某个方面的值

> **实例方法get类**
>
> getTime()：返回实例距离1970年1月1日00:00:00的毫秒数
> getDate()：返回实例对象对应每个月的几号（从1开始）
> getDay()：返回星期几，星期日为0，星期一为1，以此类推
> getYear()：返回距离1900的年数
> getFullYear()：返回四位的年份
> getMonth()：返回月份（0表示1月，11表示12月）
> getHours()：返回小时（0-23）
> getMilliseconds()：返回毫秒（0-999）
> getMinutes()：返回分钟（0-59）
> getSeconds()：返回秒（0-59）

```js
var d = new Date('January 6, 2022');
d.getDate() // 6
d.getMonth() // 0
d.getYear() // 122
d.getFullYear() // 2022
```

编写函数获得本年度剩余天数

```js
function leftDays() {
  var today = new Date();
  var endYear = new Date(today.getFullYear(), 11, 31, 23, 59, 59, 999);
  var msPerDay = 24 * 60 * 60 * 1000;
  return Math.round((endYear.getTime() - today.getTime()) / msPerDay);
}
```

**实时效果反馈**

**1. 下列代码计算本年度剩余天数，划横线处应该填写代码是：**

```js
function leftDays() {
  var today = new Date();
  var endYear = new Date(today.___, 11, 31, 23, 59, 59, 999);
  var msPerDay = 24 * 60 * 60 * 1000;
  return Math.round((endYear.___ - today.___) / msPerDay);
}
```

# DOM概述

DOM 是 JavaScript 操作网页的接口，全称为“文档对象模型”（Document Object Model）。它的作用是将网页转为一个 JavaScript 对象，从而可以用脚本进行各种操作（比如对元素增删内容）

浏览器会根据 DOM 模型，将结构化文档HTML解析成一系列的节点，再由这些节点组成一个树状结构（DOM Tree）。所有的节点和最终的树状结构，都有规范的对外接口

DOM 只是一个接口规范，可以用各种语言实现。所以严格地说，DOM 不是 JavaScript 语法的一部分，但是 DOM 操作是 JavaScript 最常见的任务，离开了 DOM，JavaScript 就无法控制网页。另一方面，JavaScript 也是最常用于 DOM 操作的语言

### 节点

DOM 的最小组成单位叫做节点（node）。文档的树形结构（DOM 树），就是由各种不同类型的节点组成。每个节点可以看作是文档树的一片叶子

<img src="https://wowpb.pages.dev/file/5e7987a4cff9cb5930c8e.png" alt="image-20211027131821279" style="zoom:50%;" />

节点的类型有七种

> Document：整个文档树的顶层节点
> DocumentType：doctype标签
> Element：网页的各种HTML标签
> Attribute：网页元素的属性（比如class="right"）
> Text：标签之间或标签包含的文本
> Comment：注释
> DocumentFragment：文档的片段

### 节点树

一个文档的所有节点，按照所在的层级，可以抽象成一种树状结构。这种树状结构就是 DOM 树。它有一个顶层节点，下一层都是顶层节点的子节点，然后子节点又有自己的子节点，就这样层层衍生出一个金字塔结构，倒过来就像一棵树

浏览器原生提供document节点，代表整个文档

```js
document
// 整个文档树
```

除了根节点，其他节点都有三种层级关系

> 父节点关系（parentNode）：直接的那个上级节点
> 子节点关系（childNodes）：直接的下级节点
> 同级节点关系（sibling）：拥有同一个父节点的节点

### Node.nodeType属性

不同节点的nodeType属性值和对应的常量如下

> 文档节点（document）：9，对应常量Node.DOCUMENT_NODE
> 元素节点（element）：1，对应常量Node.ELEMENT_NODE
> 属性节点（attr）：2，对应常量Node.ATTRIBUTE_NODE
> 文本节点（text）：3，对应常量Node.TEXT_NODE
> 文档片断节点（DocumentFragment）：11，对应常量Node.DOCUMENT_FRAGMENT_NODE

```js
document.nodeType // 9
document.nodeType === Node.DOCUMENT_NODE // true
```

## document对象_方法/获取元素

### document.getElementsByTagName()

`document.getElementsByTagName`方法搜索 HTML 标签名，返回符合条件的元素。它的返回值是一个类似数组对象（`HTMLCollection`实例），可以实时反映 HTML 文档的变化。如果没有任何匹配的元素，就返回一个空集

```js
var paras = document.getElementsByTagName('p');
```

如果传入`*`，就可以返回文档中所有 HTML 元素

```js
var allElements = document.getElementsByTagName('*');
```

### document.getElementsByClassName()

`document.getElementsByClassName`方法返回一个类似数组的对象（`HTMLCollection`实例），包括了所有`class`名字符合指定条件的元素，元素的变化实时反映在返回结果中

```js
var elements = document.getElementsByClassName(names);
```

由于`class`是保留字，所以 JavaScript 一律使用`className`表示 CSS 的`class`

参数可以是多个`class`，它们之间使用空格分隔

```js
var elements = document.getElementsByClassName('foo bar');
```

### document.getElementsByName()

`document.getElementsByName`方法用于选择拥有`name`属性的 HTML 元素（比如`<form>`、`<radio>`、`<img>`等），返回一个类似数组的的对象（`NodeList`实例），因为`name`属性相同的元素可能不止一个

```js
// 表单为 <form name="itbaizhan"></form>
var forms = document.getElementsByName('itbaizhan');
```

### document.getElementById()

`document.getElementById`方法返回匹配指定`id`属性的元素节点。如果没有发现匹配的节点，则返回`null`

```js
var elem = document.getElementById('para1');
```

注意，该方法的参数是大小写敏感的。比如，如果某个节点的`id`属性是`main`，那么`document.getElementById('Main')`将返回`null`

### document.querySelector()

`document.querySelector`方法接受一个 CSS 选择器作为参数，返回匹配该选择器的元素节点。如果有多个节点满足匹配条件，则返回第一个匹配的节点。如果没有发现匹配的节点，则返回`null`

```js
var el1 = document.querySelector('.myclass');
```

### document.querySelectorAll()

`document.querySelectorAll`方法与`querySelector`用法类似，区别是返回一个`NodeList`对象，包含所有匹配给定选择器的节点

```js
var elementList = document.querySelectorAll('.myclass');
```

## document对象_方法/创建元素

### document.createElement()

`document.createElement`方法用来生成元素节点，并返回该节点

```js
var newDiv = document.createElement('div');
```

### document.createTextNode()

`document.createTextNode`方法用来生成文本节点（`Text`实例），并返回该节点。它的参数是文本节点的内容

```js
var newDiv = document.createElement('div');
var newContent = document.createTextNode('Hello');
newDiv.appendChild(newContent);
```

### document.createAttribute()

`document.createAttribute`方法生成一个新的属性节点（`Attr`实例），并返回它

```js
var attribute = document.createAttribute(name);
```

```js
var root = document.getElementById('root');
var it = document.createAttribute('itbaizhan');
it.value = 'it';
root.setAttributeNode(it);
```

**实时效果反馈**

**1. 下列代码是创建元素，并添加内容，横线处应该填写的内容是：**

```js
var newDiv = document.createElement('div');
var newContent = document.____('Hello');
newDiv.appendChild(newContent);
```

## Element对象_属性

Element对象对应网页的 HTML 元素。每一个 HTML 元素，在 DOM 树上都会转化成一个Element节点对象（以下简称元素节点）

### Element.id

`Element.id`属性返回指定元素的`id`属性，该属性可读写

```js
// HTML 代码为 <p id="foo">
var p = document.querySelector('p');
p.id // "foo"
```

### Element.className

`className`属性用来读写当前元素节点的`class`属性。它的值是一个字符串，每个`class`之间用空格分割

```js
// HTML 代码 <div class="one two three" id="myDiv"></div>
var div = document.getElementById('myDiv');
div.className
```

### Element.classList

`classList`对象有下列方法

> - `add()`：增加一个 class。
> - `remove()`：移除一个 class。
> - `contains()`：检查当前元素是否包含某个 class。
> - `toggle()`：将某个 class 移入或移出当前元素。

```js
var div = document.getElementById('myDiv');

div.classList.add('myCssClass');
div.classList.add('foo', 'bar');
div.classList.remove('myCssClass');
div.classList.toggle('myCssClass'); // 如果 myCssClass 不存在就加入，否则移除
div.classList.contains('myCssClass'); // 返回 true 或者 false
```

### Element.innerHTML

`Element.innerHTML`属性返回一个字符串，等同于该元素包含的所有 HTML 代码。该属性可读写，常用来设置某个节点的内容。它能改写所有元素节点的内容，包括`<HTML>`和`<body>`元素

```js
el.innerHTML = '';
```

### Element.innerText

`innerText`和`innerHTML`类似，不同的是`innerText`无法识别元素，会直接渲染成字符串

**实时效果反馈**

**1. 下列代码为div元素动态添加一个class，画横线处应该填写的内容是：**

```js
var div = document.getElementById('myDiv');
div.classList.___('myCssClass');
```

## Element获取元素位置


| 属性         |                                     描述                                     |
| :----------- | :---------------------------------------------------------------------------: |
| clientHeight |          获取元素高度包括`padding`部分，但是不包括`border`、`margin`          |
| clientWidth  |          获取元素宽度包括`padding`部分，但是不包括`border`、`margin`          |
| scrollHeight | 元素总高度，它包括`padding`，但是不包括`border`、`margin`包括溢出的不可见内容 |
| scrollWidth  | 元素总宽度，它包括`padding`，但是不包括`border`、`margin`包括溢出的不可见内容 |
| scrollLeft   |                      元素的水平滚动条向右滚动的像素数量                      |
| scrollTop    |                      元素的垂直滚动条向下滚动的像素数量                      |
| offsetHeight |    元素的 CSS 垂直高度（单位像素），包括元素本身的高度、padding 和 border    |
| offsetWidth  |    元素的 CSS 水平宽度（单位像素），包括元素本身的高度、padding 和 border    |
| offsetLeft   |                            到定位父级左边界的间距                            |
| offsetTop    |                            到定位父级上边界的间距                            |

### Element.clientHeight，Element.clientWidth

`Element.clientHeight`属性返回一个整数值，表示元素节点的 CSS 高度（单位像素），只对块级元素生效，对于行内元素返回`0`。如果块级元素没有设置 CSS 高度，则返回实际高度

除了元素本身的高度，它还包括`padding`部分，但是不包括`border`、`margin`。如果有水平滚动条，还要减去水平滚动条的高度。注意，这个值始终是整数，如果是小数会被四舍五入。

`Element.clientWidth`属性返回元素节点的 CSS 宽度，同样只对块级元素有效，也是只包括元素本身的宽度和`padding`，如果有垂直滚动条，还要减去垂直滚动条的宽度。

`document.documentElement`的`clientHeight`属性，返回当前视口的高度（即浏览器窗口的高度）。`document.body`的高度则是网页的实际高度。

```js
// 视口高度
document.documentElement.clientHeight

// 网页总高度
document.body.clientHeight
```

### Element.scrollHeight，Element.scrollWidth

`Element.scrollHeight`属性返回一个整数值（小数会四舍五入），表示当前元素的总高度（单位像素），它包括`padding`，但是不包括`border`、`margin`以及水平滚动条的高度（如果有水平滚动条的话）

`Element.scrollWidth`属性表示当前元素的总宽度（单位像素），其他地方都与`scrollHeight`属性类似。这两个属性只读

整张网页的总高度可以从`document.documentElement`或`document.body`上读取

```js
// 返回网页的总高度
document.documentElement.scrollHeight
document.body.scrollHeight
```

### Element.scrollLeft，Element.scrollTop

`Element.scrollLeft`属性表示当前元素的水平滚动条向右侧滚动的像素数量，`Element.scrollTop`属性表示当前元素的垂直滚动条向下滚动的像素数量。对于那些没有滚动条的网页元素，这两个属性总是等于0

如果要查看整张网页的水平的和垂直的滚动距离，要从`document.documentElement`元素上读取

```js
document.documentElement.scrollLeft
document.documentElement.scrollTop
```

### Element.offsetHeight，Element.offsetWidth

`Element.offsetHeight`属性返回一个整数，表示元素的 CSS 垂直高度（单位像素），包括元素本身的高度、padding 和 border，以及水平滚动条的高度（如果存在滚动条）。

`Element.offsetWidth`属性表示元素的 CSS 水平宽度（单位像素），其他都与`Element.offsetHeight`一致。

这两个属性都是只读属性，只比`Element.clientHeight`和`Element.clientWidth`多了边框的高度或宽度。如果元素的 CSS 设为不可见（比如`display: none;`），则返回`0`

### Element.offsetLeft，Element.offsetTop

`Element.offsetLeft`返回当前元素左上角相对于`Element.offsetParent`节点的水平位移，`Element.offsetTop`返回垂直位移，单位为像素。通常，这两个值是指相对于父节点的位移

```html
<div class="parent">
    <div class="box" id="box"></div>
</div>
```

```css
.parent{
    width: 200px;
    height: 200px;
    background: red;
    position: relative;
    left: 50px;
    top: 50px;
}

.box{
    width: 100px;
    height: 100px;
    background: yellow;
    position: relative;
    left: 50px;
    top: 50px;
}
```

```js
var box = document.getElementById("box");
console.log(box.offsetLeft);
console.log(box.offsetTop);
```

**实时效果反馈**

**1. 下面是获得整个视口的宽度和高度：**

```js
document.documentElement.___
document.documentElement.___
```

# CSS操作

### `HTML` 元素的 `style` 属性

操作 CSS 样式最简单的方法，就是使用网页元素节点的`setAttribute`方法直接操作网页元素的`style`属性

```js
div.setAttribute(
  'style',
  'background-color:red;' + 'border:1px solid black;'
);
```

### 元素节点的`style`属性

```js
var divStyle = document.querySelector('div').style;

divStyle.backgroundColor = 'red';
divStyle.border = '1px solid black';
divStyle.width = '100px';
divStyle.height = '100px';
divStyle.fontSize = '10em';
```

### `cssText`属性

```js
var divStyle = document.querySelector('div').style;

divStyle.cssText = 'background-color: red;'
  + 'border: 1px solid black;'
  + 'height: 100px;'
  + 'width: 100px;';
```

**实时效果反馈**

**1. 下列是设置样式的代码，横线处应该填写的代码是：**

```js
var divStyle = document.querySelector('div');
divStyle.___.backgroundColor = 'red';
```

# 事件处理程序

事件处理程序分为：

1. HTML事件处理
2. DOM0级事件处理
3. DOM2级事件处理

### HTML事件

```html
<!DOCTYPE html>
<html>
    <head lang="en">
        <meta charset="UTF-8">
        <title>Js事件详解--事件处理</title>
    </head>
    <body>
        <div id="div">
            <button id="btn1" onclick="demo()">按钮</button>
        </div>
        <script>
            function demo(){
                alert("hello html事件处理");
            }
        </script>
    </body>
</html>
```

### DOM0级事件处理

```html
<body>
    <div id="div">
        <button id="btn1">按钮</button>
    </div>
    <script>
        var btn1=document.getElementById("btn1");
        btn1.onclick=function(){alert("Hello DOM0级事件处理程序1");}//被覆盖掉
        btn1.onclick=function(){alert("Hello DOM0级事件处理程序2");}
    </script>
</body>
```

### DOM2级事件处理

```html
<body>
    <div id="div">
        <button id="btn1">按钮</button>
    </div>
    <script>
        var btn1=document.getElementById("btn1");
        btn1.addEventListener("click",demo1);
        btn1.addEventListener("click",demo2);
        btn1.addEventListener("click",demo3);
        function demo1(){
            alert("DOM2级事件处理程序1")
        }
        function demo2(){
            alert("DOM2级事件处理程序2")
        }
        function demo3(){
            alert("DOM2级事件处理程序3")
        }
        btn1.removeEventListener("click",demo2);
    </script>
</body>
```

**实时效果反馈**

**1. 下列代码，是属于哪种事件处理方式：**

```js
btn2.addEventListener('click',function(){
	console.log("触发事件");
})
```

# 事件类型之鼠标事件

### 鼠标事件

鼠标事件指与鼠标相关的事件，具体的事件主要有以下一些

1. click：按下鼠标时触发
2. dblclick：在同一个元素上双击鼠标时触发
3. mousedown：按下鼠标键时触发
4. mouseup：释放按下的鼠标键时触发
5. mousemove：当鼠标在节点内部移动时触发。当鼠标持续移动时，该事件会连触发。
6. mouseenter：鼠标进入一个节点时触发，进入子节点不会触发这个事件
7. mouseleave：鼠标离开一个节点时触发，离开父节点不会触发这个事件
8. mouseover：鼠标进入一个节点时触发，进入子节点会再一次触发这个事件
9. mouseout：鼠标离开一个节点时触发，离开父节点也会触发这个事件
10. wheel：滚动鼠标的滚轮时触发

```js
var btn1 = document.getElementById("btn1");
btn1.onclick = function(){
    console.log("click事件");
}
```

> **温馨提示**
>
> 这些方法在使用的时候，除了DOM2级事件，都需要添加前缀`on`

**实时效果反馈**

**1. 下列代码是鼠标事件，划横线处应该填写的是：**

```js
// 需求：鼠标在元素内移动，会一直触发事件
var box = document.getElementById("box");
box.___ = function(){
    console.log("事件在元素上会一直触发");
}
```

# Event事件对象

事件发生以后，会产生一个事件对象，作为参数传给监听函数。

### Event对象属性

1. Event.Target
2. Event.type

#### Event.target

Event.target属性返回事件当前所在的节点

```js
// HTML代码为
// <p id="para">Hello</p>
function setColor(e) {
  console.log(this === e.target);
  e.target.style.color = 'red';
}

para.addEventListener('click', setColor);
```

#### Event.type

Event.type属性返回一个字符串，表示事件类型。事件的类型是在生成事件的时候。该属性只读

### Event对象方法

1. Event.preventDefault()
2. Event.stopPropagation()

#### Event.preventDefault

Event.preventDefault方法取消浏览器对当前事件的默认行为。比如点击链接后，浏览器默认会跳转到另一个页面，使用这个方法以后，就不会跳转了

```js
btn.onclick = function(e){
    e.preventDefault(); // 阻止默认事件
    console.log("点击A标签");
}
```

#### Event.stopPropagation()

stopPropagation方法阻止事件在 DOM 中继续传播，防止再触发定义在别的节点上的监听函数，但是不包括在当前节点上其他的事件监听函数

```js
btn.onclick = function(e){
    e.stopPropagation(); // 阻止事件冒泡
    console.log("btn");
}
```

# 事件类型之键盘事件

键盘事件由用户击打键盘触发，主要有keydown、keypress、keyup三个事件

1. keydown：按下键盘时触发。
2. keypress：按下有值的键时触发，即按下 Ctrl、Alt、Shift、Meta 这样无值的键，这个事件不会触发。对于有值的键，按下时先触发keydown事件，再触发这个事件。
3. keyup：松开键盘时触发该事件

```js
username.onkeypress = function(e){
    console.log("keypress事件");
}
```

### event对象

keyCode:唯一标识

```js
var username = document.getElementById("username");
username.onkeydown = function(e){
    if(e.keyCode === 13){
        console.log("回车");
    }
}
```

# 事件类型之表单事件

表单事件是在使用表单元素及输入框元素可以监听的一系列事件

1. input事件
2. select事件
3. Change事件
4. reset事件
5. submit事件

### input事件

input事件当`<input>、<select>、<textarea>`的值发生变化时触发。对于复选框（`<input type=checkbox>`）或单选框（`<input type=radio>`），用户改变选项时，也会触发这个事件

input事件的一个特点，就是会连续触发，比如用户每按下一次按键，就会触发一次input事件。

```js
var username = document.getElementById("username");
username.oninput = function(e){
    console.log(e.target.value);
}
```

### select事件

select事件当在`<input>、<textarea>`里面选中文本时触发

```js
// HTML 代码如下
// <input id="test" type="text" value="Select me!" />

var elem = document.getElementById('test');
elem.addEventListener('select', function (e) {
  console.log(e.type); // "select"
}, false);
```

### Change 事件

Change事件当`<input>、<select>、<textarea>`的值发生变化时触发。它与input事件的最大不同，就是不会连续触发，只有当全部修改完成时才会触发

```js
var email = document.getElementById("email");
email.onchange = function(e){
    console.log(e.target.value);
}
```

### reset 事件，submit 事件

这两个事件发生在表单对象`<form>`上，而不是发生在表单的成员上。

reset事件当表单重置（所有表单成员变回默认值）时触发。

submit事件当表单数据向服务器提交时触发。注意，submit事件的发生对象是`<form>`元素，而不是`<button>`元素，因为提交的是表单，而不是按钮

```html
<form id="myForm" onsubmit="submitHandle">
    <button onclick="resetHandle">重置数据</button>
    <button>提交</button>
</form>
```

```js
var myForm = document.getElementById("myForm")
function resetHandle(){
    myForm.reset();
}
function submitHandle(){
    console.log("提交");
}
```

**实时效果反馈**

**1. 下列代码中，横线处应该填写的内容是：**

```js
// 需求：用户每次输入都需要触发的事件
var username = document.getElementById("username");
username.___ = function(e){
    console.log(e.target.value);
}
```

# 事件代理(事件委托)

由于事件会在冒泡阶段向上传播到父节点，因此可以把子节点的监听函数定义在父节点上，由父节点的监听函数统一处理多个子元素的事件。这种方法叫做事件的代理（delegation）

```js
var ul = document.querySelector('ul');

ul.addEventListener('click', function (event) {
  if (event.target.tagName.toLowerCase() === 'li') {
    // some code
  }
});
```

**实时效果反馈**

**1. 下列是关于事件代理的处理，横线处应该填写的代码是：**

```js
var parent = document.getElementById("parent");
parent.addEventListener("click",function(e){
    if(e.___.tagName.___ === 'li'){
        console.log(e.target.innerHTML);
    }
})
```

# 定时器之`setTimeout()`

JavaScript 提供定时执行代码的功能，叫做定时器（timer），主要由`setTimeout()`和`setInterval()`这两个函数来完成。它们向任务队列添加定时任务

`setTimeout`函数用来指定某个函数或某段代码，在多少毫秒之后执行。它返回一个整数，表示定时器的编号，以后可以用来取消这个定时器。

```js
var timerId = setTimeout(func|code, delay);
```

`setTimeout`函数接受两个参数，第一个参数`func|code`是将要推迟执行的函数名或者一段代码，第二个参数`delay`是推迟执行的毫秒数

```js
setTimeout(function(){
    console.log("定时器")
},1000)
```

> **温馨提示**
>
> 还有一个需要注意的地方，如果回调函数是对象的方法，那么`setTimeout`使得方法内部的`this`关键字指向全局环境，而不是定义时所在的那个对象

```js
var name = "sxt";
var user = {
    name: "itbaizhan",
    getName: function () {
        setTimeout(function(){
            console.log(this.name);
        },1000)
    }
};
user.getName();
```

解决方案

```js
var name = "sxt";
var user = {
    name: "itbaizhan",
    getName: function () {
        var that = this;
        setTimeout(function(){
            console.log(that.name);
        },1000)
    }
};
user.getName();
```

定时器可以进行取消

```js
var id = setTimeout(f, 1000);
clearTimeout(id);
```

**实时效果反馈**

**1. 以下是关于定时器`setTimeout`代码，运行结果是：**

```js
var name = "sxt";
var user = {
    name: "itbaizhan",
    getName: function () {
        var that = this;
        setTimeout(function(){
            console.log(that.name);
        },1000)
    }
};
user.getName();
```

# 定时器之`setInterval()`

`setInterval`函数的用法与`setTimeout`完全一致，区别仅仅在于`setInterval`指定某个任务每隔一段时间就执行一次，也就是无限次的定时执行

```js
var timer = setInterval(function() {
  console.log(2);
}, 1000)
```

通过setInterval方法实现网页动画

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<title>Document</title>
	<style>
		#someDiv{
			width: 100px;
			height: 100px;
			background: red;
		}
	</style>
</head>
<body>
	<div id="someDiv"></div>
	<script>
		var div = document.getElementById('someDiv');
		var opacity = 1;
		var fader = setInterval(function() {
		  opacity -= 0.05;
		  if (opacity > 0) {
		    div.style.opacity = opacity;
		  } else {
		    clearInterval(fader);
		  }
		}, 30);

	</script>
</body>
</html>
```

定时器可以进行取消

```js
var id = setInterval(f, 1000);
clearInterval(id);
```

**实时效果反馈**

**1. 以下是关于定时器`setInterval`代码，每秒打印当前时间，横线处填写代码是：**

```js
___(function(){
    console.log(new Date().toLocaleTimeString());
},1000)
```

# 防抖(debounce)

防抖严格算起来应该属于性能优化的知识，但实际上遇到的频率相当高，处理不当或者放任不管就容易引起浏览器卡死。

从滚动条监听的例子说起

```js
function showTop  () {
    var scrollTop = document.documentElement.scrollTop;
    console.log('滚动条位置：' + scrollTop);
}
window.onscroll  = showTop
```

在运行的时候会发现存在一个问题：这个函数的默认执行频率，太！高！了！。 高到什么程度呢？以chrome为例，我们可以点击选中一个页面的滚动条，然后点击一次键盘的【向下方向键】，会发现函数执行了8-9次！

然而实际上我们并不需要如此高频的反馈，毕竟浏览器的性能是有限的，不应该浪费在这里，所以接着讨论如何优化这种场景。

基于上述场景，首先提出第一种思路：在第一次触发事件时，不立即执行函数，而是给出一个期限值比如200ms，然后

1. 如果在200ms内没有再次触发滚动事件，那么就执行函数
2. 如果在200ms内再次触发滚动事件，那么当前的计时取消，重新开始计时

效果：如果短时间内大量触发同一事件，只会执行一次函数

实现：既然前面都提到了计时，那实现的关键就在于setTimeout这个函数，由于还需要一个变量来保存计时，考虑维护全局纯净，可以借助闭包来实现

```js
function debounce(fn,delay){
    let timer = null //借助闭包
    return function() {
        if(timer){
            clearTimeout(timer)
        }
        timer = setTimeout(fn,delay) // 简化写法
    }
}
// 然后是旧代码
function showTop  () {
    var scrollTop = document.documentElement.scrollTop;
    console.log('滚动条位置：' + scrollTop);
}
window.onscroll = debounce(showTop,300)
```

到这里，已经把防抖实现了

> **防抖定义**
>
> 对于短时间内连续触发的事件（上面的滚动事件），防抖的含义就是让某个时间期限（如上面的1000毫秒）内，事件处理函数只执行一次

**实时效果反馈**

**1. 以下是关于定时器防抖代码，横线处填写代码是：**

```js
function debounce(fn,delay){
    let timer = null
    return function() {
        if(timer){
            _2_(timer)
        }
        timer = _1_(fn,delay)
    }
}
function showTop  () {
    var scrollTop = document.documentElement.scrollTop;
    console.log('滚动条位置：' + scrollTop);
}
window.onscroll = debounce(showTop,300)
```

# 节流(throttle)

节流严格算起来应该属于性能优化的知识，但实际上遇到的频率相当高，处理不当或者放任不管就容易引起浏览器卡死

继续思考，使用上面的防抖方案来处理问题的结果是

如果在限定时间段内，不断触发滚动事件（比如某个用户闲着无聊，按住滚动不断的拖来拖去），只要不停止触发，理论上就永远不会输出当前距离顶部的距离

但是如果产品同学的期望处理方案是：即使用户不断拖动滚动条，也能在某个时间间隔之后给出反馈呢？

其实很简单：我们可以设计一种类似控制阀门一样定期开放的函数，也就是让函数执行一次后，在某个时间段内暂时失效，过了这段时间后再重新激活（类似于技能冷却时间）

效果：如果短时间内大量触发同一事件，那么在函数执行一次之后，该函数在指定的时间期限内不再工作，直至过了这段时间才重新生效

**实现**

这里借助setTimeout来做一个简单的实现，加上一个状态位valid来表示当前函数是否处于工作状态

```js
function throttle(fn,delay){
    let valid = true
    return function() {
       if(!valid){
           //休息时间 暂不接客
           return false
       }
       // 工作时间，执行函数并且在间隔期内把状态位设为无效
        valid = false
        setTimeout(function(){
            fn()
            valid = true;
        }, delay)
    }
}

function showTop  () {
    var scrollTop = document.documentElement.scrollTop;
    console.log('滚动条位置：' + scrollTop);
}
window.onscroll = throttle(showTop,300)
```

如果一直拖着滚动条进行滚动，那么会以300ms的时间间隔，持续输出当前位置和顶部的距离

讲完了这两个技巧，下面介绍一下平时开发中常遇到的场景:

1. 搜索框input事件，例如要支持输入实时搜索可以使用节流方案（间隔一段时间就必须查询相关内容），或者实现输入间隔大于某个值（如500ms），就当做用户输入完成，然后开始搜索，具体使用哪种方案要看业务需求
2. 页面resize事件，常见于需要做页面适配的时候。需要根据最终呈现的页面情况进行dom渲染（这种情形一般是使用防抖，因为只需要判断最后一次的变化情况）

**实时效果反馈**

**1. 以下是关于定时器节流代码，横线处填写代码是：**

```js
function throttle(fn,delay){
    let valid = true
    return function() {
       if(_1_){
           return false
       }
        valid = false
        _2_(function(){
            fn()
            valid = true;
        }, delay)
    }
}
```
