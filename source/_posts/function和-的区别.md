---
title: function和=>的区别
date: 2018-02-04 21:33:54
tags: 主页
---

有次在元素onclick事件中使用 `箭头函数`打印this,却不是当前的元素对象，而是window，本就好奇心严重的我就带着疑问去找原因

```js
document.querySelector('p').onclick = () => {
    console.log(this)
}
// window
```

<!-- more -->
### 箭头函数 Arrow functions ：箭头函数与现有函数不同，但并不是用来替代现有函数的，他一般是用来作为回调函数使用的，主要目的是为了简化回调函数的写法：

- 箭头函数本身是没有this的，函数内的this执行箭头函数 定义时所在的对象，而不是使用时所在的对象，

- 箭头函数内部，不存在arguments对象

- 不可以当作构造函数，不可以使用new指令

- 简化回调函数

```js
document.querySelector('p').onclick = () => {
    console.log(this)
}
// windoiw  使用箭头函数自身是没有this值的

var x = '1';
var set = x => {
	console.log(x)
	console.log(arguments)
}
set(2)
// 2
// ReferenceError: arguments is not defined
// 箭头函数没有arguments值


function set(x){
	console.log(x)
}
var s = new set(1)
// 1

set = x => {
	console.log(x)
}
var s = new set(1)
// Uncaught TypeError: set is not a constructor
// 一般函数可以用new指令，但是箭头函数不能使用new指令
```