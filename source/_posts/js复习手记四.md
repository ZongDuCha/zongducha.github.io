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

### Element.matches() 如果元素包含指定的选择器字符串，存在返回true，不存在则返回false

#### 目前在非标准名称 `matchesSelector()` 实现了这个方法，并且不同浏览器使用前需要前缀。

- el.matchesSelector( DOMElement element, String selector )

```js
<div class="main"></div>

....
// chrome 下
var body = document.querySelector('.main')
var ifAtt = body.webkitMatchesSelector('.main')
console.log(ifAtt)
// true
```

为了方便兼容IE8+既低版本的浏览器使用，可以重写matchesSelector方法

```js
if (!Element.prototype.matchesSelector) {
    Element.prototype.matchesSelector = 
    Element.prototype.matchesSelector || 
    Element.prototype.mozMatchesSelector ||
    Element.prototype.msMatchesSelector || 
    Element.prototype.oMatchesSelector || 
    Element.prototype.webkitMatchesSelector ||
    (function(){
            var matches = (this.document || this.ownerDocument).querySelectorAll(s),
            i = matches.length;
            while (--i >= 0 && matches.item(i) !== this) {}
            return !!i;
     }
    )();
}


var body = document.querySelector('.main')
var ifAtt = body.matchesSelector('.main')
console.log(ifAtt)
```

需要注意的是：如果指定的选择器是数字开头的则会报错，如class="1mian",id="1main"

参考文献：https://www.lyblog.net/detail/601.html  ,   https://developer.mozilla.org/zh-CN/docs/Web/API/Element/matches


### getComputedStyle和currentStyle

element.style 获取的是内联样式或设置样式 , 如果获取指定的属性名  style中不存在则返回空

element.currentStyle 这是ie专有的属性，只在ie下支持，在获取未设置的属性值时,currentStyle会读取浏览器的默认值

document.defaultView.getComputedStyle(element,null) ie6~ie8是不支持的，获取属性和currentStyle类似

```js
<div style="height:100px"></div>

// js
var div = document.querySelector('div')
div.style.height
// "100px"
div.style.width
// ""


div.currentStyle['height']
// "100px"
div.currentStyle['width']
// "0px"

document.defaultView.getComputedStyle(div,null).height
// "100px"
document.defaultView.getComputedStyle(div,null).backgroundColor
// "rgba(0, 0, 0, 0)",js中属性名是需要驼峰写法
document.defaultView.getComputedStyle(div,null)['background-color']
// "rgba(0, 0, 0, 0)"，数组中字符串可以不用驼峰写法


// 兼容写法
Element.prototype.getComputedStyle = function(x){
    return getComputedStyle(this)[x] || this.currentStyle[x] || this.style[x]
}
div.getComputedStyle('height')
// "100px"
```