---
title: js复习手记七
date: 2018-04-24 23:33:47
tags: 主页
---

## 事件模型

<!-- more -->

冒泡事件： 当使用事件冒泡的时候，子元素先触发，然后父元素后触发

捕获事件： 当使用时间捕获的时候，父元素先触发，然后子元素后触发

dom时间流： 同时支持冒泡和捕获事件，先触发捕获机制然后在冒泡机制

阻止冒泡： 在w3c中设置使用`stopPropagation()`方法；在IE下设置 `cancelBubble = true`.

阻止默认事件： 阻止事件默认的行为，在w3c中使用 `preventDefault()`方法；在IE下设置`window.event.returnValue = false`



## new操作符内部具体干了什么

1. 创建一个空对象
2. 设置原型链，将赋值变量的__proto__指向新对象的prototype，继承属性和方法
3. 将新的对象this指向引用的对象，就好像使用了call或apply
4. 判断函数返回值的类型，如果是对象，就会返回对象的内容，如果不是就返回这个引用类型的对象


## Ajax原理

Ajax的原理就是服务器和客户端之间加了个中间层（ajax引擎），通过xmlhttpRequest对象对服务器发起异步请求，从服务器获取数据，然后用js操作dom更新页面内容，实现用户操作与服务器响应异步化

![](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1524670684736&di=d4ec4bc8d6c9acc21962c474591a1ee3&imgtype=0&src=http%3A%2F%2Ffilesimg.111cn.net%2F2011%2F05%2F20111119011732771.jpg%3F16)


## 几种跨域方案

1. 通过jsonp跨域
2. document.domain + iframe跨域
3. location.hash + iframe
4. window.name + iframe跨域
5. postMessage跨域
6. 跨域资源共享（CORS）
7. nginx代理跨域
8. nodejs中间件代理跨域
9. WebSocket协议跨域


## 实现模块化开发

 模块化就是讲js文件按照功能分离，根据需求引入不同的文件中。

 优点：
 
 1.防止命名冲突
 2.减少文件依赖
 3.异步加载文件


 立即执行函数,不暴露私有成员

 ```js
var module1 = (function(){
　　　　var _count = 0;
　　　　var m1 = function(){
　　　　　　console.log(1)
　　　　};
　　　　var m2 = function(){
　　　　　　console.log(2)
　　　　};
　　　　return {
　　　　　　m1 : m1,
　　　　　　m2 : m2
　　　　};
})();
 ```

 ## 异步加载js方式
 1.动态生成<script>标签
 
 ```js
(function(){
    function lazy(){
        var s=document.createElement("script");
        s.type="text/javascript";
        s.src="http://china-addthis.googlecode.com/svn/trunk/addthis.js";
        document.body.appendChild(s);
    }
    window.addEventListener("load",lazy,false);
})();
```
 
 2.defer（同步，与其他文件加载时并行的）
 <script type="text/javascript" src='http://china-addthis.googlecode.com/svn/trunk/addthis.js' defer="defer"></script>
defer属性规定是否对脚本执行进行延迟，直到页面加载为止。之前只有IE的hack支持defer属性，现在H5开始全面支持defer属性。

 3.async（异步，会打乱执行顺序）
 <script type="text/javascript" src='http://china-addthis.googlecode.com/svn/trunk/addthis.js' async="async"></script>
 async是HTML5的新属性，该属性规定一旦脚本可用，则会异步执行（一旦下载完毕就会立刻执行）。需要注意的是：async属性仅适用于外部脚本（只有在使用src属性时）。

4.XHR注入

5.ajax eval

6.iframe

## XML和JSON的区别

1. JSON 比 XML数据体积小，传输速度快，容易解析处理，数据容易交互

3. JSON 比 XML 不好理解数据格式

4. JSON的速度要远远快于XML

