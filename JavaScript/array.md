## 特性
数组是一段线性分配的内存，它通过整数计算偏移并访问其中的元素，JavaScript没有像此类数组一样的数据结构．

JavaScript中的数组是一种拥有一些类数组特性的对象，其第一个值将获得整数属性名'0'

> Any object that is not an ordinary object is an exotic object.

```js
var arr = [];
console.assert(arr[0] === arr['0'])
```
```js
console.assert([] == ![]);

// [] is truthy
console.assert(!![] === true);
console.assert([] == false);
```

只存在整数属性的对象不存在length属性,只存在非整数属性的数组length为0．数组下标的表达式最终被转换为字符串.

```js
var arr = [];
arr['a'] = 1;
arr['2'] = 2;
console.assert(arr.length === 3);
arr.length = 0;
console.assert(arr['a'] === 'a');
```

## isArray
```js
if (Type(argument) is not Object)
  return false;
if (argument is an Array exotic object)
  return true;
if (argument is a Proxy exotic object) {
  ......
}

return false;
```

## Array.prototype
```js
var stack = [1, 2];
stack.pop();
var queue = [1, 2];
queue.shift();

var elems = document.querySelectorAll('select option:checked');
var values = Array.prototype.map.call(elems, function (obj) {
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
  return [-1, 0, 1][?];// SpiderMonkey can deal with true&false, v8 can not.
});

// 若deleteCount被省略, 则默认被赋值为array.length - start
Array.prototype.splice(start, deleteCount, itemN);
```

## ES2015+ Array constructor
```js
Array.of(1, 2, 3);
// polyfill
Array.of = function () {
  return Array.prototype.slice.call(arguments);
};

Array.from([1, 2, 3], x => x + x);
Array.from({length: 5}, (v, i) => i);
```

## ES2015+ Array prototype
```js
var str = '12345';
var str1 = [...str].reverse().join();
// desugar
var str2 = [].slice.call(str).reverse().join();

const [...iterableObj] = [1, 3, 5, 7, 9];
console.log([0, 2, ...iterableObj, 4, 6, 8]);

let arr = [1, 2, 3];
let arr2 = [...arr]; // arr.slice();

var arr1 = [0, 1, 2];
var arr2 = [3, 4, 5];
arr1.push(...arr2); // Array.prototype.push.apply(arr1, arr2);
arr1.concat(arr2); // 不改变参与运算的数组

var nodeList = document.querySelectorAll('div');
var array = [...nodeList];
```

## lodash
```js
_.uniq([1, 2, 1]);
function uniq(array) {
  return (array && array.length) ? baseUniq(array) : [];
}

_.uniqBy([{ age: 18 }, { age: 18 }, [1], [1]], 'age');
function uniqBy(array, iteratee) {
  return (array && array.length) ? baseUniq(array, getIteratee(iteratee, 2)) : [];
}

_.uniqWith([{ age: 18 }, { age: 18 }, [1], [1]], _.isEqual);
function uniqWith(array, comparator) {
  comparator = typeof comparator == 'function' ? comparator : undefined;
  return (array && array.length) ? baseUniq(array, undefined, comparator) : [];
}
```
