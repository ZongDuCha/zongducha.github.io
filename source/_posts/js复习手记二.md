---
title: js复习手记二
date: 2018-01-24 14:04:23
tags: 主页
---
### `concat`  `join`  `slice`  `toString`  `toLocateString`  `indexOf`  `lastIndexOf`  都是不会改变自身的数组方法


### concat 将传入的数组或元素与原数组合并,组成新的数组并返回

语法：arr.concat(value1, value2, …, valueN)

```javascript
var arr = [1,2]
var arr2 = arr.concat([3,4],[5,6])
console.log(arr2)

// [1, 2, 3, 4, 5, 6]
```

若concat不传入参数，那么将基于原数组浅复制生成一个一样的新数组（指向新的地址空间）
```javascript
var arr = [1,2]
var arr2 = arr.concat()
arr2.push(1)
console.log(arr,arr2)

// [1, 2] [1, 2, 1]
```
<!-- more -->

同样，concat也可以用Array.prototype.concat.call(),但是结果却不是我们想要的

```javascript
var arr = {1:'q',2:'w',length:2}
var arr2 = Array.prototype.concat.call(arr,'q',{3:'e',length:1})
console.log(arr2)

// 0:{1: "q", 2: "w", length: 2}
// 1:"q"
// 2:{3: "e", length: 1}
```
类数组对象合并后返回的是依然是数组，并不是我们想要的结果


### join 将数组中的所有元素连接成一个字符串

语法：arr.join([separator = ‘,’]) separator可选，缺省默认为逗号。

```javascript
var arr = ['js','css','jq']
var arr2 = arr.join()
var arr3 = arr.join('-')
var arr4 = arr.join(' 1 ')
console.log(arr2,arr3,arr4)

// js,css,jq js-css-jq js 1 css 1 jq
```

同样，join也可以用Array.prototype.join.call()

```javascript
var arr = ['js','css','jq']
var arr2 = Array.prototype.join.call(arr,'-')
console.log(arr2)

// js-css-jq
```


### slice 将数组的一部分浅复制存入新的数组对象里,并且返回这个数组对象

语法：arr.slice([start[, end]])

start 从数组的索引开始提取元素，如果传入的参数为负数，则表示从原数组中的末尾倒数第几个元素，如果省略，那么slice的索引为0开始。
end 结束数组的索引位置，并且提取start到end的索引（不包括end）如果被省略或者end大于数组的索引长度，slice会一直提取到原数组的末尾（相当于从start到数组的最后一个元素）

按照MDN的对slice的描述：

```
slice 不修改原数组，只会返回一个浅复制了原数组中的元素的一个新数组。原数组的元素会按照下述规则拷贝：

如果该元素是个对象引用 （不是实际的对象），slice 会拷贝这个对象引用到新的数组里。两个对象引用都引用了同一个对象。如果被引用的对象发生改变，则新的和原来的数组中的这个元素也会发生改变。

对于字符串、数字及布尔值来说（不是 String、Number 或者 Boolean 对象），slice 会拷贝这些值到新的数组里。在别的数组里修改这些字符串或数字或是布尔值，将不会影响另一个数组。

如果向两个数组任一中添加了新元素，则另一个不会受到影响。
```

```javascript
var arr = ['js','css','jq']
var arr2 = arr.slice(0,1)
arr2.push('vue')
console.log(arr,arr2)
// ["js", "css", "jq"] (2) ["js", "vue"]
```

slice 也适用于Array.prototype.slice.call()

```javascript
var arr = [1,2,3]
var arr2 = Array.prototype.slice.call(arr,0,1)
console.log(arr2)
// [1]
```

另外不支持的可以用polyfill

```javascript
(function () {
    'use strict';
    var _slice = Array.prototype.slice;

    try {
        // Can't be used with DOM elements in IE < 9
        _slice.call(document.documentElement);
    } catch (e) { // Fails in IE < 9
        // This will work for genuine arrays, array-like objects,
        // NamedNodeMap (attributes, entities, notations),
        // NodeList (e.g., getElementsByTagName), HTMLCollection (e.g., childNodes),
        // and will not fail on other DOM objects (as do DOM elements in IE < 9)
        Array.prototype.slice = function (begin, end) {
            // IE < 9 gets unhappy with an undefined end argument
            end = (typeof end !== 'undefined') ? end : this.length;

            // For native Array objects, we use the native slice function
            if (Object.prototype.toString.call(this) === '[object Array]'){
                return _slice.call(this, begin, end);
            }
           
            // For array like object we handle it ourselves.
            var i, cloned = [],
                size, len = this.length;
           
            // Handle negative value for "begin"
            var start = begin || 0;
            start = (start >= 0) ? start: len + start;
           
            // Handle negative value for "end"
            var upTo = (end) ? end : len;
            if (end < 0) {
                upTo = len + end;
            }
           
            // Actual expected size of the slice
            size = upTo - start;
           
            if (size > 0) {
                cloned = new Array(size);
                if (this.charAt) {
                    for (i = 0; i < size; i++) {
                        cloned[i] = this.charAt(start + i);
                    }
                } else {
                    for (i = 0; i < size; i++) {
                        cloned[i] = this[start + i];
                    }
                }
            }
           
            return cloned;
        };
    }
}());
```

### toString 返回数组的字符串形式，该字符串由数组中的每个元素的toString()返回值经调用join()方法连接（由逗号隔开）组成。（相当于toString就是用arr.join()实现的）

```javascript
var arr = [1,2,3]
var arr2 = arr.toString()
console.log(arr2)

// 1,2,3
```

当数组直接和字符串做连接操作时，会自动调用toString方法
```js
[1,2,3,4] + [,5]

// 1,2,3,4,5

var arr = {1:'q',2:'w',3:'e',length:4}
var arr2 = Array.prototype.toString.call(arr)
console.log(arr2)
// [object Object]
```

根据ES5语义，toString() 方法是通用的，可被用于任何对象。
如果对象有一个join() 方法，将会被调用，其返回值将被返回，没有则调用Object.prototype.toString()，为此，我们给o对象添加一个join方法。如下：

```js
var o = {
  0:'Jan', 
  1:'Feb', 
  2:'Mar', 
  length:3, 
  join:function(){
    return Array.prototype.join.call(this);
  }
};
console.log(Array.prototype.toString.call(o));
```

### <a href="#toLocalString" name="toLocalString">toLocalString</a> 类似toString的变形，该字符串由数组中的每个元素的 toLocaleString() 返回值经调用 join() 方法连接（由逗号隔开）组成。

数组不同的元素将调用各自的toLocalString方法

- Object: Object.prototype.toLocalString
- Number: Number.prototype.toLocalString
- Date : Date.prototype.toLocalString


```js
var o = {
  0:123, 
  1:'abc', 
  2:new Date(), 
  length:3, 
  join:function(){
    return Array.prototype.join.call(this);
  }
};
console.log(Array.prototype.toLocaleString.call(o))
// 123,abc,2018/1/24 下午4:13:45
```

### indexOf 查找元素在数组中第一次出现的索引位置，如果没有，则返回-1

语法：arr.indexOf(element, fromIndex=0)

element 需要查找的元素

fromIndex 为开始查找的位置，缺省默认为0，如果超出数组的长度则返回-1。
如果为负值，假设数组长度为length，则从数组的第length+fromIndex项开始往末尾查找，如果length+fromIndex<0则整个数组都会被查找。

indexOf使用严格相等（即===去匹配数组中的元素）


```js
var array = ['abc', 'def', 'ghi','123'];
console.log(array.indexOf('def')); // 1
console.log(array.indexOf('def',-1)); // -1 此时表示从最后一个元素往后查找,因此查找失败返回-1
console.log(array.indexOf('def',-4)); // 1 由于4大于数组长度,此时将查找整个数组,因此返回1
console.log(array.indexOf(123)); // -1, 由于是严格匹配,因此并不会匹配到字符串'123'
```

同样可以用Array.prototype.indexOf.call()

```js
var o = {0:'abc', 1:'def', 2:'ghi', length:3};
console.log(Array.prototype.indexOf.call(o,'ghi',-4));

// 2
```

如果不支持，可以用polyfill
```js
if (!Array.prototype.indexOf) {
  Array.prototype.indexOf = function(searchElement, fromIndex) {
    var k;

    // 1. Let O be the result of calling ToObject passing
    //    the this value as the argument.
    if (this == null) {
      throw new TypeError('"this" is null or not defined');
    }

    var O = Object(this);

    // 2. Let lenValue be the result of calling the Get
    //    internal method of O with the argument "length".
    // 3. Let len be ToUint32(lenValue).
    var len = O.length >>> 0;

    // 4. If len is 0, return -1.
    if (len === 0) {
      return -1;
    }

    // 5. If argument fromIndex was passed let n be
    //    ToInteger(fromIndex); else let n be 0.
    var n = +fromIndex || 0;

    if (Math.abs(n) === Infinity) {
      n = 0;
    }

    // 6. If n >= len, return -1.
    if (n >= len) {
      return -1;
    }

    // 7. If n >= 0, then Let k be n.
    // 8. Else, n<0, Let k be len - abs(n).
    //    If k is less than 0, then let k be 0.
    k = Math.max(n >= 0 ? n : len - Math.abs(n), 0);

    // 9. Repeat, while k < len
    while (k < len) {
      if (k in O && O[k] === searchElement) {
        return k;
      }
      k++;
    }
    return -1;
  };
}
```



### lastIndexOf indexOf的逆向查找，即从数组的末尾处一直到数组的开头开始查找，查找元素最后一次出现时的索引位置，如果没有则返回-1.

语法：arr.lastIndexOf(element, fromIndex=length-1)

element 需要查找的元素

fromIndex 开始查找的位置，缺省默认为length-1，如果超过数组长度，犹豫是逆向查找，则查找整个数组，如果为负值，则从数组的第length+fromIndex项往数组开头开始查找，如果length|fromIndex<0数组则不会被查找。（与indexOf相反）

同indexOf一样，lastIndexOf也是严格匹配数组元素


```js
var arr = [1,2,3,4,5,6,7,8]
var arr2 = arr.lastIndexOf(6)
console.log(arr2)
// 5

var arr = [1,2,3,6,6,5,7,8]
var arr2 = arr.lastIndexOf(6)
console.log(arr2)
// 4

var arr = [1,2,3,6,6,6,7,8]
var arr2 = arr.lastIndexOf(6)
console.log(arr2)
// 5

var o = {0:'abc', 1:'def', 2:'ghi', length:3};
console.log(Array.prototype.lastIndexOf.call(o,'ghi'));

// 2
```

如果不支持，可以用polyfill

```js
if (!Array.prototype.lastIndexOf) {
  Array.prototype.lastIndexOf = function(searchElement /*, fromIndex*/) {
    'use strict';

    if (this === void 0 || this === null) {
      throw new TypeError();
    }

    var n, k,
      t = Object(this),
      len = t.length >>> 0;
    if (len === 0) {
      return -1;
    }

    n = len - 1;
    if (arguments.length > 1) {
      n = Number(arguments[1]);
      if (n != n) {
        n = 0;
      }
      else if (n != 0 && n != (1 / 0) && n != -(1 / 0)) {
        n = (n > 0 || -1) * Math.floor(Math.abs(n));
      }
    }

    for (k = n >= 0 ? Math.min(n, len - 1) : len - Math.abs(n); k >= 0; k--) {
      if (k in t && t[k] === searchElement) {
        return k;
      }
    }
    return -1;
  };
}
```



### includes(ES7) 基于ECMAScript 2016(es7)规范，他用于查找数组中是否包含某个指定的值，如果是则返回true，否则返回false

语法：arr.includes(element, fromIndex=0)

element 查找的元素

fromIndex 数组索引开始的位置,缺省为0，他是正向查找，即从索引处往数组末尾开始查找。

```js
var arr = [-0,1,2,3,4,5]
var arr2 = arr.includes(4)
var arr3 = arr.includes(-4)
var arr4 = arr.includes(2,1)
var arr5 = arr.includes(+0)
console.log(arr2,arr3,arr4,arr5)

// true,false,true,true
```

以上includes 忽略了-0和+0的区别，因为JavaScript一直以来都是不区分 -0 和 +0 的。但是只有0才不区分而已。

includes 与 indexOf 相似,但是又有一些区别。
一、index 返回的是数值型，而include返回的是布尔值,在某些时候include更好使用
```js
var ary = [1];

if (ary.indexOf(1) !== -1) {

    console.log("数组存在1")

}

if (ary.includes(1)) {

    console.log("数组存在1")

}
```

二、如果数组中有NaN，你又正好需要判断数组中是否有NaN的存在，indexOf是无法判断的，而includes却是可以的
```js
var ary1 = [NaN];

console.log(ary1.indexOf(NaN))//-1

console.log(ary1.includes(NaN))//true
```

三、当数组中有空的值时候，includeOf会认为空的值是undefined,而indexOf认为是稀疏数组，省略掉的值是不存在的
```js
var ary1 = new Array(3);

console.log(ary1.indexOf(undefined));//-1

console.log(ary1.includes(undefined))//true
```


同样适用Array.prototype.includes.call()
```js
var o = {0:'a', 1:'b', 2:'c', length:3};
var bool = Array.prototype.includes.call(o, 'a');
console.log(bool); 

// true
```

polyfill

```js
if (!Array.prototype.includes) {
  Object.defineProperty(Array.prototype, 'includes', {
    value: function(searchElement, fromIndex) {

      if (this == null) {
        throw new TypeError('"this" is null or not defined');
      }

      // 1. Let O be ? ToObject(this value).
      var o = Object(this);

      // 2. Let len be ? ToLength(? Get(O, "length")).
      var len = o.length >>> 0;

      // 3. If len is 0, return false.
      if (len === 0) {
        return false;
      }

      // 4. Let n be ? ToInteger(fromIndex).
      //    (If fromIndex is undefined, this step produces the value 0.)
      var n = fromIndex | 0;

      // 5. If n ≥ 0, then
      //  a. Let k be n.
      //  6. Else n < 0,
      //  a. Let k be len + n.
      //  b. If k < 0, let k be 0.
      var k = Math.max(n >= 0 ? n : len - Math.abs(n), 0);

      function sameValueZero(x, y) {
        return x === y || (typeof x === 'number' && typeof y === 'number' && isNaN(x) && isNaN(y));
      }

      // 7. Repeat, while k < len
      while (k < len) {
        // a. Let elementK be the result of ? Get(O, ! ToString(k)).
        // b. If SameValueZero(searchElement, elementK) is true, return true.
        if (sameValueZero(o[k], searchElement)) {
          return true;
        }
        // c. Increase k by 1. 
        k++;
      }

      // 8. Return false
      return false;
    }
  });
}
```


### toSource（非标准） 返回数组的源代码

```js
var array = ['a', 'b', 'c'];
console.log(array.toSource()); // ["a", "b", "c"]

// Array.prototype.toSource.call()
var o = {0:'a', 1:'b', 2:'c', length:3};
console.log(Array.prototype.toSource.call(o));

// // ["a","b","c"]
```


参考资料1：http://louiszhai.github.io/2017/04/28/array
参考资料2：https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array