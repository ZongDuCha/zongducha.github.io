---
title: js实现继承方式及特点
date: 2018-04-21 12:49:25
tags: 主页
---

## js实现继承及特点

<!-- more -->


##### 原型链继承（prototype）

当子函数的prototype对象指向父函数，那么子函数就能继承父函数，最常见的一种方式

```js
function a(){
	this.name = ['js']
}
function b(){
	this.age = 123
}
b.prototype = new a()
b.prototype.constructor = b
// 最好也加上
c = new b()
console.log(c.name,c.age) // ['js'] 123
```

简单继承

缺点明显：new一个新的实例的时候，所有引用类型（数组，对象，函数等等）的属性被修改后，其它所继承原型的都会受到影响

```js
// ...上面省略

c = new b()
d = new b()
c.name.push('qwe')
console.log(c.name,d.name) // ["js", "qwe"]  ["js", "qwe"]
```


##### 构造函数绑定（子函数内使用apply或call）

借用父类的构造函数来增强子类实例，等于是把父类的实例属性复制了给子类

```js
function a(){
	this.name = ['js']
}
function b(){
	a.apply(this,arguments)
	this.age = 123
}
c = new b()
d = new b()
c.name.push(1)
console.log(c.name,d.name) // ["js", 1] ["js"]
```

可以向父类的函数传参

解决了子类实例共享父类引用属性的问题

缺点：

函数无法复用，每个子类实例都有一个新的函数，太多影响性能


##### 组合式继承 （构造函数+原型链）

把实例函数都放在原型对象上，以实现函数复用。同时还要保留借用构造函数方式的优点，

a.call(this);继承父类的基本属性和引用属性并保留能传参的优点；

a.prototype = new Super();继承父类函数，实现函数复用

```js
function a(){
	this.name = ['js'];
	this.age = function(q){
		console.log(q)
	}
}
function b(){
	a.call(this)
}
b.prototype = new a()
b.prototype.constructor = b
c = new b()
d = new b()
c.name.push(2)
console.log(c.name,d.name) // ["js", 2] ["js"]
```

- 解决了子类实例共享父类引用属性的问题

- 可以向父类的函数传参

- 函数可复用

缺点：

子类原型有多余的父类实例属性，因为父类构造函数了调用了两次

依然浪费内存，不过比构造函数继承 好些


##### 新对象组合继承（也叫寄生组合式继承）

看了阮一峰的博客明白许多.

需要在子类上添加 `b.uber.constructor.call(this)` , b.uber在定义extend方式时定义了，等同于继承了父类的prototype (a.prototype)。

如果父类需要传入参数，可以在子类这样写：b.uber.constructor.call(this,name)

（uber是一个德语词，意思是"向上"、"上一层"。）这等于在子对象上打开一条通道，可以直接调用父对象的方法。这一行放在这里，只是为了实现继承的完备性，纯属备用性质。

```js
function extend(Child, Parent) {
	var F = function(){};
	F.prototype = Parent.prototype;
	Child.prototype = new F();
	Child.prototype.constructor = Child;
	Child.uber = Parent.prototype; // 此处是一个自定义扩展属性，不一定非得用uber，目的是在定义子类时调用
}


function a(){
	this.name = ['js']
}
function b(){
	b.uber.constructor.call(this) 
	this.age = '123'
}
extend(b,a)
c = new b()
d = new b()
c.name.push(12)
console.log(c.name,d.name) // ["js", 12] ["js"]
```


参考文献：https://www.zhihu.com/question/22232912
参考文献：https://www.cnblogs.com/ayqy/p/4471638.html
参考文献：http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_inheritance.html
参考文献：https://segmentfault.com/a/1190000008457844
参考文献：http://web.zhaicool.net/443.html