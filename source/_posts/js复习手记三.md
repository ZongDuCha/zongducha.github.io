---
title: js复习手记三
date: 2018-01-24 17:28:46
tags: 主页
---

### forEach  every  some  filter  map  reduce  reduceRight以及es6新增的entries  find  findIndex keys  values 都是基于es6，不会改变自身的办法一共有12个。

### forEach 方法指定数组的每项元素都执行一次传入函数，返回值为undefined.
语法：arr.forEach(fn, thisArg)

fn 标识在数组每一项上执行的函数，接收三个参数。
- value 当前正在被处理的元素的值
- index 当前正在被处理元素的索引
- array 当前数组的本身

thisArg 可选，用来当作fn函数内的this对象

forEach 将为数组中的每一项执行一次fn函数，那些已删除，新增或者从未赋值的项将被跳过（但不包括值为undefined的项）

遍历过程中，fn会被传入上述三个参数

<!-- more -->
```js
var arr =[1,2,3,4]
var obj = {name:'cc'}
arr.forEach(function(value,index,array){
	arr[index] = value * 2
	console.log(this.name)
	console.log(value)
},obj)
// cc
// 1
// cc
// 2
// cc
// 3
// cc
// 4
// obj.name执行了四次
```

同样也可以用Array.prototype.forEach.call()
```js
var o = {0:1, 1:3, 2:5, length:3};
Array.prototype.forEach.call(o,function(value, index, obj){
  console.log(value,index,obj);
  obj[index] = value * value;
},o);
```

如果不支持可以用polyfill

```js
if (!Array.prototype.forEach) {

  Array.prototype.forEach = function(callback, thisArg) {

    var T, k;

    if (this == null) {
      throw new TypeError(' this is null or not defined');
    }

    // 1. Let O be the result of calling toObject() passing the
    // |this| value as the argument.
    var O = Object(this);

    // 2. Let lenValue be the result of calling the Get() internal
    // method of O with the argument "length".
    // 3. Let len be toUint32(lenValue).
    var len = O.length >>> 0;

    // 4. If isCallable(callback) is false, throw a TypeError exception. 
    // See: http://es5.github.com/#x9.11
    if (typeof callback !== "function") {
      throw new TypeError(callback + ' is not a function');
    }

    // 5. If thisArg was supplied, let T be thisArg; else let
    // T be undefined.
    if (arguments.length > 1) {
      T = thisArg;
    }

    // 6. Let k be 0
    k = 0;

    // 7. Repeat, while k < len
    while (k < len) {

      var kValue;

      // a. Let Pk be ToString(k).
      //    This is implicit for LHS operands of the in operator
      // b. Let kPresent be the result of calling the HasProperty
      //    internal method of O with argument Pk.
      //    This step can be combined with c
      // c. If kPresent is true, then
      if (k in O) {

        // i. Let kValue be the result of calling the Get internal
        // method of O with argument Pk.
        kValue = O[k];

        // ii. Call the Call internal method of callback with T as
        // the this value and argument list containing kValue, k, and O.
        callback.call(T, kValue, k, O);
      }
      // d. Increase k by 1.
      k++;
    }
    // 8. return undefined
  };
}
```

### every 传入的函数测试所有的元素，只要其中有一个函数返回值为false，那么该办法的结果为false；如果全部返回true，那么该办法的结果才返回true，因为every存在如下规律：

- 若需检测数组中所有存在元素是否大于10（即value > 10），那么需要在传入的函数中return 'true'返回值，同时整个方法结果为true才表示数组存在的元素满足条件 （即数组中的value 都大于10，那么就算满足条件，返回true）
- 反之 若需要检测数组中存在的元素是否有不满足的大于10的，返回 'false'

语法同forEach 相同， 

```js
var arr = [11,12,13,14,15,16]
var arr2 = [1,11,12,13,14,15]
arr.every(function(value){
	console.log(value > 10)
})
arr2.every(function(value){
	console.log(value > 10)
})

// true false
// arr2 因为数组第一个元素不满足 条件，所以返回false
```

同样也适用Array.prototype.every.call()

```js
var o = {0:10, 1:8, 2:25, length:3};
var bool = Array.prototype.every.call(o,function(value, index, obj){
  return value >= 8;
},o);
console.log(bool);
```

如果不支持，可以用ployfill

```js
if (!Array.prototype.every)
{
  Array.prototype.every = function(fun /*, thisArg */)
  {
    'use strict';

    if (this === void 0 || this === null)
      throw new TypeError();

    var t = Object(this);
    var len = t.length >>> 0;
    if (typeof fun !== 'function')
        throw new TypeError();

    var thisArg = arguments.length >= 2 ? arguments[1] : void 0;
    for (var i = 0; i < len; i++)
    {
      if (i in t && !fun.call(thisArg, t[i], i, t))
        return false;
    }

    return true;
  };
}
```

### some 跟every相反，使用some检测数组元素时，只要有一个函数返回值为true，则该方法返回true，如果全部都返回false,则该方法才返回false，

- 若需检测数组中存在的元素是否有一个元素或多个元素大于10（即value > 10）,那么我们需要在传入的函数中 return 'true'，则some返回true,反之所有函数的返回false，则some返回false

```js
var arr = [10,1,2,3,4]
var arr2 = [1,2,3,4,5]
var arr3 = arr.some(function(value){
	return value >= 10
})
var arr4 = arr2.some(function(value){
	return value >= 10
})
console.log(arr3,arr4)

// true false
```

Array.prototype.come.call()同every一样，参照every的写法。

如果不支持的话可以用polyfill:

```js
if (!Array.prototype.some)
{
  Array.prototype.some = function(fun /*, thisArg */)
  {
    'use strict';

    if (this === void 0 || this === null)
      throw new TypeError();

    var t = Object(this);
    var len = t.length >>> 0;
    if (typeof fun !== 'function')
      throw new TypeError();

    var thisArg = arguments.length >= 2 ? arguments[1] : void 0;
    for (var i = 0; i < len; i++)
    {
      if (i in t && fun.call(thisArg, t[i], i, t))
        return true;
    }

    return false;
  };
}
```

### filter 使用传入的函数测试所有元素，并返回所有通过测试的元素组成新的数组，他就好比一个过滤器，筛选掉不符合条件的元素。

语法：arr.filter(fn, thisArg)

```js
var arr = [10,3,20,12,13,40]
var arr2 = arr.filter(function(value){
	return value >= 10 && value <= 20
})
console.log(arr2)
// [10, 20, 12, 13]
// 满足大于等于10且小于等于20，并输出
```
Array.prototype.filter.call()写法同every一样，polyfill:

```js
if (!Array.prototype.filter)
{
  Array.prototype.filter = function(fun /* , thisArg*/)
  {
    "use strict";

    if (this === void 0 || this === null)
      throw new TypeError();

    var t = Object(this);
    var len = t.length >>> 0;
    if (typeof fun !== "function")
      throw new TypeError();

    var res = [];
    var thisArg = arguments.length >= 2 ? arguments[1] : void 0;
    for (var i = 0; i < len; i++)
    {
      if (i in t)
      {
        var val = t[i];

        // NOTE: Technically this should Object.defineProperty at
        //       the next index, as push can be affected by
        //       properties on Object.prototype and Array.prototype.
        //       But that method's new, and collisions should be
        //       rare, so use the more-compatible alternative.
        if (fun.call(thisArg, val, i, t))
          res.push(val);
      }
    }

    return res;
  };
}
```


### map 遍历数组元素，使用传入函数处理每一个元素，并返回函数的返回值组成新的数组。

语法：
let new_array = arr.map(function callback(currentValue, index, array) { 
    // Return element for new_array 
}[, thisArg])

- map方法会给数组中的每个元素都按顺序调用一次callback函数，callback每次执行后返回的返回值（包括undefined）组合成新的数组，callback函数只会在有有值的索引上被调用，那些从来没被赋过值或者使用delete删除的索引则不会使用。
- callback 函数会被自动传入三个参数，数组元素，数组索引，原数组本身
- thisArg 参数有值，则每次callback函数被调用的时候，this都会指向这个thisArg参数上的这个对象，如果省略了thisArg参数，或者赋值为null或undefined，则this指向全局对象。
- map在执行的的时候不会修改原数组本身 （也可以在callback执行时改变原数组元素）
- 使用 map 方法处理数组时，数组元素的范围是在 callback 方法第一次调用之前就已经确定了。在 map 方法执行的过程中：原数组中新增加的元素将不会被 callback 访问到；若已经存在的元素被改变或删除了，则它们的传递到 callback 的值是 map 方法遍历到它们的那一时刻的值；而被删除的元素将不会被访问到。

```js
var obj = [{key:1,value:10},{key:2,value:20},{key:3,value:30}]
var obj2 = obj.map(function(value){
	var robj = {};
	robj[value.key] = value.value;
	return robj
})
console.log(obj2)

// 0:{1: 10}
// 1:{2: 20}
// 2:{3: 30}


// console.log(obj)
// 0:{key: 1, value: 10}
// 1:{key: 2, value: 20}
// 2:{key: 3, value: 30}
// 原远足未被修改
```

Array.prototype.map.cal()使用:
```js
var a = Array.prototype.map.call('hello word',function(value){
	console.log(value)
})


h
e
l
l
o

w
o
r
d
```

遍历querySelectorAll()所有集合
```js
var el = document.querySelectorAll('div')
Array.prototype.map.call(el,function(value){
	console.log(value)
})
```

返回字符串
```js
var str = '12345';
Array.prototype.map.call(str, function(x) {
  return x;
}).reverse().join(''); 

// 输出: '54321'
```

MDN上有这么描述map的话：

通常情况下，map 方法中的 callback 函数只需要接受一个参数，就是正在被遍历的数组元素本身。但这并不意味着 map 只给 callback 传了一个参数。这个思维惯性可能会让我们犯一个很容易犯的错误。

```js
// 下面的语句返回什么呢:
["1", "2", "3"].map(parseInt);
// 你可能觉的会是[1, 2, 3]
// 但实际的结果是 [1, NaN, NaN]

// 通常使用parseInt时,只需要传递一个参数.
// 但实际上,parseInt可以有两个参数.第二个参数是进制数.
// 可以通过语句"alert(parseInt.length)===2"来验证.
// map方法在调用callback函数时,会给它传递三个参数:当前正在遍历的元素, 
// 元素索引, 原数组本身.
// 第三个参数parseInt会忽视, 但第二个参数不会,也就是说,
// parseInt把传过来的索引值当成进制数来使用.从而返回了NaN.

function returnInt(element) {
  return parseInt(element, 10);
}

['1', '2', '3'].map(returnInt); // [1, 2, 3]
// 意料之中的结果

// 也可以使用简单的箭头函数，结果同上
['1', '2', '3'].map( str => parseInt(str) ); // [1, 2, 3]

// 一个更简单的方式:
['1', '2', '3'].map(Number); // [1, 2, 3]
// 与`parseInt` 不同，下面的结果会返回浮点数或指数:
['1.1', '2.2e2', '3e300'].map(Number); // [1.1, 220, 3e+300]
```


### reduce 接收一个方法作为累加器，数组中的每个值（从左至右）开始合并，最终一个值。

语法：arr.reduce(callback, initialValue)

callback 每次执行数组中每个值的函数，包含四个参数:

- previousValue 上一次调用回调时累计的返回的值，或者是提供的初始值
- value 数组中当前被处理元素的值
- index 当前元素在数组中的索引
- array 原数组自身

initialValue 指定第一次调用fn的第一个参数，如果没有提供初始值，则使用数组的第一个元素的值，。

当callback第一次执行时：
- 如果initialValue 在调用reduce时被提供，那么第一个previousValue 将等于initialValue，此时item等于数组中的第一个值
- 如果 initialValue 未被提供，那么 previousVaule 等于数组中的第一个值，item 等于数组中的第二个值。此时如果数组为空，那么将抛出 TypeError。
- 如果数组仅有一个元素，并且没有提供 initialValue，或提供了 initialValue 但数组为空，那么fn不会被执行，数组的唯一值将被返回。

计算1到900的和
```js
var arr = []
for(var i=0;i<=1000;i++){
	arr.push(i)
	if(i == 900){
		var arr2 = arr.reduce(function(sum,value){
			return sum+value
		})
	}
}
console.log(arr2)
// 405450
```

把数组的[1,2,3,4,5]转换成整数的12345
```js
var num = [1,2,3,4,5].reduce(function(x,y){
	return x.toString() + y
})
console.log(num)
// 12345
```

数组去除重复
```js
var arr = [1,2,3,2,1,33,3,2,1]
var arr2 = arr.sort().reduce(function(init,current){
	if(init.length === 0 || init[init.length-1]!== current){
		init.push(current);
	}
  return init;
},[])
console.log(arr2)
// [1, 2, 3, 33]
```

```js
var array = [1, 2, 3, 4];
var s = array.reduce(function(previousValue, value, index, array){
  return previousValue * value;
},1);
console.log(s); // 24
// ES6写法更加简洁
array.reduce((p, v) => p * v); // 24
```

以上回调被执行了4次，每次的参数和返回：
|callback	|previousValue	|currentValue	|index	|array	|return |value|
|---|---|---|---|---|---|---|
|第1次	|1	|1	|1	|[1,2,3,4]	1|
|第2次	|1	|2	|2	|[1,2,3,4]	2|
|第3次	|2	|3	|3	|[1,2,3,4]	6|
|第4次	|6	|4	|4	|[1,2,3,4]	24|

Array.prototype.reduce.call()写法参照every，如果不支持reduce,可以用polyfill:

```js
if (!Array.prototype.reduce) {
  Object.defineProperty(Array.prototype, 'reduce', {
    value: function(callback /*, initialValue*/) {
      if (this === null) {
        throw new TypeError( 'Array.prototype.reduce ' + 
          'called on null or undefined' );
      }
      if (typeof callback !== 'function') {
        throw new TypeError( callback +
          ' is not a function');
      }

      // 1. Let O be ? ToObject(this value).
      var o = Object(this);

      // 2. Let len be ? ToLength(? Get(O, "length")).
      var len = o.length >>> 0; 

      // Steps 3, 4, 5, 6, 7      
      var k = 0; 
      var value;

      if (arguments.length >= 2) {
        value = arguments[1];
      } else {
        while (k < len && !(k in o)) {
          k++; 
        }

        // 3. If len is 0 and initialValue is not present,
        //    throw a TypeError exception.
        if (k >= len) {
          throw new TypeError( 'Reduce of empty array ' +
            'with no initial value' );
        }
        value = o[k++];
      }

      // 8. Repeat, while k < len
      while (k < len) {
        // a. Let Pk be ! ToString(k).
        // b. Let kPresent be ? HasProperty(O, Pk).
        // c. If kPresent is true, then
        //    i.  Let kValue be ? Get(O, Pk).
        //    ii. Let accumulator be ? Call(
        //          callbackfn, undefined,
        //          « accumulator, kValue, k, O »).
        if (k in o) {
          value = callback(value, o[k], k, o);
        }

        // d. Increase k by 1.      
        k++;
      }

      // 9. Return accumulator.
      return value;
    }
  });
}
```


### reduceRight 方法与reduce写法一样，与reduce不同的是，reduceRight是从右至左开始合并，最终返回一个值，与reduce的执行方向相反，其它完全一致，写法参考reduce

polyfill:
```js
if ('function' !== typeof Array.prototype.reduceRight) {
  Array.prototype.reduceRight = function(callback /*, initialValue*/) {
    'use strict';
    if (null === this || 'undefined' === typeof this) {
      throw new TypeError('Array.prototype.reduce called on null or undefined');
    }
    if ('function' !== typeof callback) {
      throw new TypeError(callback + ' is not a function');
    }
    var t = Object(this), len = t.length >>> 0, k = len - 1, value;
    if (arguments.length >= 2) {
      value = arguments[1];
    } else {
      while (k >= 0 && !(k in t)) {
        k--;
      }
      if (k < 0) {
        throw new TypeError('Reduce of empty array with no initial value');
      }
      value = t[k--];
    }
    for (; k >= 0; k--) {
      if (k in t) {
        value = callback(value, t[k], k, t);
      }
    }
    return value;
  };
}
```


### entries 基于ECMAScript2015(es6)规范，返回一个数组迭代器对象，该对象包括数组中每个索引的键值对,手动循环对象的属性值可以输出相应的属性位置的值。

```js
var arr = [1,2,3,4]
var arr2 = arr.entries()
console.log(arr2.next().value)
console.log(arr2.next().value)
console.log(arr2.next().value)
console.log(arr2.next().value)
console.log(arr2.next().value)

// [0, 1]
// [1, 2]
// [2, 3]
// [3, 4]
// undefined 数组结尾在迭代就是undefined
```

也可以使用：Array.prototype.entries.call() 

```js
var o = {0:"a", 1:"b", 2:"c", length:3};
var iterator = Array.prototype.entries.call(o);
console.log(iterator.next().value); // [0, "a"]
console.log(iterator.next().value); // [1, "b"]
console.log(iterator.next().value); // [2, "c"]
```


### find 基于ECMAScript2015(es6)规范，返回数组中第一个满足条件的元素的值，如果数组为空或是稀疏数组 则返回undefined，find不会改变原来的数组。

语法：arr.find(callback[, thisArg])

- callback 执行数组的每一个值的函数，接收3个参数
- element 当前遍历到的元素
- index 当前遍历到的索引
- array 数组本身
- thisArg 可指定callback的this参数

查找满足条件的数组对象
```js
var inventory = [
    {value: 'js', name: 0},
    {value: 'css', name: 1},
    {value: 'jq', name: 2}
];

function findCherries(fruit) { 
    return fruit.value === 'css';
}

function findIndexCher(isValue){
  return isValue === 'vue'
}

console.log(inventory.find(findCherries));  // {value: "css", name: 1}
console.log(inventory.find(findIndexCher)); // undefined
```

Array.prototype.find.call()写法与上面类似，不在阐述，

polyfill:
```js
if (!Array.prototype.find) {
  Object.defineProperty(Array.prototype, 'find', {
    value: function(predicate) {
     // 1. Let O be ? ToObject(this value).
      if (this == null) {
        throw new TypeError('"this" is null or not defined');
      }

      var o = Object(this);

      // 2. Let len be ? ToLength(? Get(O, "length")).
      var len = o.length >>> 0;

      // 3. If IsCallable(predicate) is false, throw a TypeError exception.
      if (typeof predicate !== 'function') {
        throw new TypeError('predicate must be a function');
      }

      // 4. If thisArg was supplied, let T be thisArg; else let T be undefined.
      var thisArg = arguments[1];

      // 5. Let k be 0.
      var k = 0;

      // 6. Repeat, while k < len
      while (k < len) {
        // a. Let Pk be ! ToString(k).
        // b. Let kValue be ? Get(O, Pk).
        // c. Let testResult be ToBoolean(? Call(predicate, T, « kValue, k, O »)).
        // d. If testResult is true, return kValue.
        var kValue = o[k];
        if (predicate.call(thisArg, kValue, k, o)) {
          return kValue;
        }
        // e. Increase k by 1.
        k++;
      }

      // 7. Return undefined.
      return undefined;
    }
  });
}
```


### findIndex 基于ECMASscript2015(es6)规范，他返回数组中第一个满足条件的元素的索引，如果没有则返回-1，写法与find相同。

```js
var inventory = [
    {value: 'js', name: 0},
    {value: 'css', name: 1},
    {value: 'jq', name: 2}
];

function findCherries(fruit) { 
    return fruit.value === 'css';
}

function findIndexCher(isValue){
  return isValue === 'vue'
}

console.log(inventory.find(findCherries));  // 1
console.log(inventory.find(findIndexCher)); // -1
```

Array.prototype.findIndex.call()写法与上面类似，不在阐述，


polyfil:
```js
if (!Array.prototype.findIndex) {
  Object.defineProperty(Array.prototype, 'findIndex', {
    value: function(predicate) {
     // 1. Let O be ? ToObject(this value).
      if (this == null) {
        throw new TypeError('"this" is null or not defined');
      }

      var o = Object(this);

      // 2. Let len be ? ToLength(? Get(O, "length")).
      var len = o.length >>> 0;

      // 3. If IsCallable(predicate) is false, throw a TypeError exception.
      if (typeof predicate !== 'function') {
        throw new TypeError('predicate must be a function');
      }

      // 4. If thisArg was supplied, let T be thisArg; else let T be undefined.
      var thisArg = arguments[1];

      // 5. Let k be 0.
      var k = 0;

      // 6. Repeat, while k < len
      while (k < len) {
        // a. Let Pk be ! ToString(k).
        // b. Let kValue be ? Get(O, Pk).
        // c. Let testResult be ToBoolean(? Call(predicate, T, « kValue, k, O »)).
        // d. If testResult is true, return k.
        var kValue = o[k];
        if (predicate.call(thisArg, kValue, k, o)) {
          return k;
        }
        // e. Increase k by 1.
        k++;
      }

      // 7. Return -1.
      return -1;
    }
  });
}
```


### keys 基于ECMAScript2015(es6)规范，返回一个数组索引的迭代器

```js
var arr = ['js','css','jq']
var iterator = arr.keys()
console.log(iterator.next()) // {value: 0, done: false}
console.log(iterator.next()) // {value: 1, done: false}
console.log(iterator.next()) // {value: 2, done: false}
console.log(iterator.next()) // {value: undefined, done: true}
```

在稀疏数组中使用keys也会 包含那些没有值的对应索引，如下：

```js
var array = ["abc", , "xyz"];
var sparseKeys = Object.keys(array);
var denseKeys = [...array.keys()];
console.log(sparseKeys); // ["0", "2"]
console.log(denseKeys);  // [0, 1, 2]
```

用keys快速生成0到10的新数组
```js
[...Array(10).keys()]; // [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
[...new Array(10).keys()]; // [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

Array.prototype.keys.call()写法与上面类似，不在阐述，


### values  基于ECMAScript2015(es6)规范，返回一个数组迭代器对象，该对象包含数组中每个索引的值。用法与entries相同。

```js
var array = ["abc", "xyz"];
var iterator = array.values();
console.log(iterator.next().value);//abc
console.log(iterator.next().value);//xyz
```


### Symbol.iterator 该方法基于ECMAScript 2015（ES6）规范，同 values 方法功能相同。

```js
var array = ["abc", "xyz"];
var iterator = array[Symbol.iterator]();
console.log(iterator.next().value); // abc
console.log(iterator.next().value); // xyz
```

Array.prototype[Symbol.iterator].call()写法与上面类似，不在阐述


参考资料1：http://louiszhai.github.io/2017/04/28/array
参考资料2：https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array