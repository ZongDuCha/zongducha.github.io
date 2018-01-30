---
title: css-Grid
date: 2018-01-02 18:05:21
tags: 主页
---
<blockquote>[首发于：](https://github.com/ZongDuCha/ZD-1.0)https://github.com/ZongDuCha/ZD-1.0
[掘金：](https://juejin.im/post/5a374a905188257d38440e4b)https://juejin.im/post/5a374a905188257d38440e4b</blockquote>


display:gri

最近时间在了解css3 中不太常用的grid布局，但是却又非常的好用

以前不管我们怎么布局，大多数用的都是一维布局，在复杂的场景下非常难用

<!-- more --> 

而grid使用的是类似与table的二维布局



(grid 兼容情况)

一开始父元素声明布局为grid

.grid{ display:grid; } 
grid-template-columns
grid-template-rous
然后使用grid-template-columns 定义列, grid-template-rous 定义行 ,创建一个4行4列的布局

```css
.grid{ 
    border: 1px solid #000;     width: 350px;    margin: 0 auto;     display:grid;
    grid-template-columns: 1fr 1fr 1fr 1fr;     grid-template-rows: 50px 50px 50px 50px; 
} 
```


fr单位可以帮助我们创建一个弹列的网格轨道。它代表了网格容器中可用的空间（就像Flexbox中无单位的值）

用浏览器查看效果（我用的是谷歌）

可以看到已经有4行4列的效果了



可以指定某行的宽度或高度，比如第二列的第二行高度为50%，宽度为50%

```
grid-template-columns: 1fr 50% 1fr 1fr; 
grid-template-rows: 50px 100px 50px 50px;
```


还可以使用grid-gap给相邻的网络增加10px的缝隙

```css
.grid{ 
    border: 1px solid #000; 
    width: 95%;
    margin: 0 auto; 
    display:grid;
    grid-template-columns: 1fr 50% 1fr 1fr; 
    grid-template-rows: 20px 50px 20px 20px; 
    grid-gap:10px;
} 
```

![](https://user-gold-cdn.xitu.io/2017/12/18/16068aa63fff6e38?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



在grid容器里写一个grid-list的div 

```
.grid-list{ grid-column:4; grid-row:2; } 
```


可以看到grid-column：4把位置放在了父容器网格列中的第四列

而grid-row：2放在了父容器网格行的第二行。

而且grid-column，grid-row很有意思，写多几个div

```html
<div class="grid">    
    <div class="grid-list">1</div>    
    <div class="grid-list">2</div>    
    <div class="grid-list">3</div>
</div>

.grid-list:nth-child(1){    grid-column:7;    grid-row:7;}
.grid-list:nth-child(2){    grid-column:6;    grid-row:6;}
.grid-list:nth-child(3){    grid-column:5;    grid-row:5;}
```
![](https://user-gold-cdn.xitu.io/2017/12/18/16068b00766ad58c?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)


可以看到子元素定义grid-column,grid-row可以改变在网格的位置

可以用斜线（开头/结尾）这样写

```css
.grid-list:nth-child(1){ grid-column: 1/5;}
 .grid-list:nth-child(2){ grid-column: 1;grid-row:2/4}
.grid-list:nth-child(3){ grid-column: 3/5;grid-row: 3/5;}
```

![](https://user-gold-cdn.xitu.io/2017/12/18/16068b4b50f0643a?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)
![](https://user-gold-cdn.xitu.io/2017/12/18/16068b6f3a7477aa?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)
![](https://user-gold-cdn.xitu.io/2017/12/18/16068b6d294a2c5f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)






同样的方式有

grid-column-start （行的开头）               grid-column-end（行的结尾）

grid-row-start （列的开头）                     grid-row-end（列的结尾）


```css
.grid-list:nth-child(1){ grid-column-start:1;grid-column-end:5} 
.grid-list:nth-child(2){ grid-column-start:1;grid-row-start:2;grid-row-end:4}
.grid-list:nth-child(3){ grid-column-start: 3;grid-column-end:5;grid-row-start: 3;grid-row-end: 5;}
```
结果跟上面的一样，感觉比较复杂

个人心得，未来的布局应该会大部分使用grid，现在赶紧学习学习



<blockquote>在ie11下grid不能使用，有什么办法呢？
使用：https://github.com/FremyCompany/css-grid-polyfill    （目前还是1.0）</blockquote>
