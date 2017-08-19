## 特性
数组是一段线性分配的内存，它通过整数计算偏移并访问其中的元素，JavaScript没有像此类数组一样的数据结构．

JavaScript中的数组是一种拥有一些类数组特性的对象，其第一个值将获得整数属性名'0'

只存在整数属性的对象不存在length属性,只存在非整数属性的数组length为0．数组下标的表达式最终被转换为字符串

```js
var arr = [];
arr['a'] = 1;
arr['2'] = 2;
console.assert(arr.length === 3);
arr.length = 0;
console.assert(arr['a'] === 'a');
```

## Array.prototype
```js
var elems = document.querySelectorAll('select option:checked');
var values = Array.prototype.map.call(elems, function(obj) {
  return obj.value;
});

// Array.prototype.reduce(若提供了initialValue, currentIndex从0开始, 否则从1开始)
[0, 1, 2, 3, 4].reduce(function(accumulator, currentValue, currentIndex, array){
  return accumulator + currentValue;
}, 10);
[[1, 2], [3, 4], [5, 6]].reduce((a, c) => [...a, ...c]);
['a', 'b', 'c', 'a'].reduce((a, c) => {
  if (c in a) {
    a[c]++;
  } else {
    a[c] = 1;
  }

  return a;
}, {});

Array.prototype.slice();
Array.prototype.slice(start, end);// [start, end)

Array.prototype.sort(function (a, b) {
  // SpiderMonkey can deal with true&false, v8 can not.
  return [-1, 0, 1][?];
});

// 若deleteCount被省略, 则默认被赋值为array.length - start
Array.prototype.splice(start, deleteCount, itemN);

```

## ES2015+
```js
var str = '12345';
var str1 = [...str].reverse().join();
// desugar
var str2 = [].slice.call(str).reverse().join();

const [...iterableObj] = [1, 3, 5, 7, 9];

var arr1 = [0, 1, 2];
var arr2 = [3, 4, 5];
arr1.push(...arr2);
// desugar
arr1.push.apply(arr1, arr2);

[...arr1, ...arr2];
// desugar
[].concat(arr1, arr2);

var nodeList = document.querySelectorAll('div');
var array = [...nodeList];
```
