---
title: "JavasSript常用方法"
date: 2024-10-22T13:29:04+08:00
categories: ['Docs']
author: "Ronan"
---
# 获取 DOM 节点

### 1.选择匹配到的第一个元素

**语法：**

```javascript
document.querySelector('css选择器')
```

**参数：**

包含一个或多个有效的css选择器 **字符串** ，查看 [更多css选择器](https://www.runoob.com/cssref/css-selectors.html)。

**返回值：**

CSS选择器匹配的第一个元素，**一个HTML Element对象**。

### 2.选择匹配的多个元素

**语法：**

```javascript
document.querySelectorAll('CSS选择器')
```

**参数：**

包含一个或多个有效的css选择器 **字符串** ，查看 [更多css选择器](https://www.runoob.com/cssref/css-selectors.html)。

**返回值：**

CSS选择器匹配的 **NodeList 对象集合**

**示例：**

```javascript
const headings = document.querySelectorAll('h1, h2, h3, h4, h5, h6');
console.log(headings[0].textContent);
```

这将打印出 h1 标签里的文本内容。

### 总结

1.获取页面中的标签我们最终常用那两种方式?

> querySelectorAll()
>
> querySelector()

2.他们两者的区别是什么?

> querySelector() 只能选择一个元素，可以直接操作
>
> querySelectorAll() 可以选择多个元素，得到的是伪数组，需要遍历得每一个元素

3.他们两者小括号里面的参数有神马注意事项

> 里面写css选择
>
> **必须是字符串，也就是必须加引号**

# 更新 DOM 节点

### 1.innerText方法

**语法：**

```javascript
对象.innerText = '<strong>这不会变成粗体</strong>'
```

**注意：**

- 通过该方法插入的只是字符串(纯文本信息)，不能识别 html 标签

### 2.innerHtml方法（最常用）

**语法：**

```javascript
对象.innerHtml = '<strong>这里可以变成粗体</strong>'
```

该方法可以识别 html 标签，或者可以用于插入 css 样式。

**示例：**

```javascript
对象.innerHtml = '
    div {
        width: 300px;
        border: 25px solid green;
        padding: 25px;
        margin: 25px;
    }
'
```

# 创建和插入节点

### 1.创建节点并添加属性

**创建节点语法：**

```javascript
document.createElement('元素')

//创建一个 p 标签
const p = document.createElement('p')
```

**添加节点属性语法：**

```javascript
对象.setAttribute('属性','属性值')

//为 p 标签添加一个 id ， id 值为 js
const p = document.createElement('p')
p.setAttribute('id','js')
```

### 2.插入节点

```javascript
对象.appendChild('节点')

//一个 p 标签插入到页面的 body 标签里面
const body = document.querySelector('body')
body.appendChild('p')

//也可以直接使用一行代码搞定
document.querySelector('body').appendChild('p')
```

# 修改元素样式属性

### 1.通过style属性操作css

**语法：**

```javascript
对象.style.样式属性 = 值
```

**示例：**

```javascript
const box = document.querySelector('.box')
box.style.width = '100px'

//多组单词(也就是属性有-链接符)采用小驼峰命名法 例如style里的 background-color 属性
box.style.backgroundColor = 'pink'
```

### 2.通过类名(className)操作css

**语法：**

```javascript
// active是一个css类名
元素.className = 'active'
```

**注意：**

- 由于class是关键字，所以使用className去代替
- className是使用新值换旧值，如果需要添加一个类，需要保留之前的类名

也就是说，假设之前已有一个类名是 nav ，如果使用上面的方法，active 会把 nav 直接覆盖。要在保留 nav 类的基础上追加 active 类，可以使用以下方法：

```javascript
元素.className = 'nav active'
```

### 3.通过 classList 操作类控制 css

- 为了解决 className 容易覆盖以前的类名，我们可以通过 classList 方式追加和删除类名

**语法：**

```javascript
// 追加一个类
元素.classList.add('类名')
// 删除一个类
元素.classList.remove('类名')
// 切换一个类
元素.classList.toggle('类名')
```
