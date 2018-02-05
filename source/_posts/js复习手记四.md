---
title: js复习手记四
date: 2018-02-04 15:56:16
tags: 主页
---

### 闭包 会持有父方法的局部变量并且不会随着父方法的销毁而销毁,闭包就是在提供了一个在外部访问另一个函数内部局部变量的方式。
<!-- more -->

```js
// 不使用闭包
function add(){
    var cont = 0;
    return cont += 1;
}
add()
add()
add()
// 1
// 1
// 1

// 闭包
var tin;
var add = function(){
    var cont = 0;
    tin = function(){return cont += 1;};
    return tin
}();

add()
add()
add()
// 1
// 2
// 3
```

