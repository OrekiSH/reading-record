## 特性
数组是一段线性分配的内存，它通过整数计算偏移并访问其中的元素，JavaScript没有像此类数组一样的数据结构．

JavaScript中的数组是一种拥有一些类数组特性的对象，其第一个值将获得整数属性名'0'

> Any object that is not an ordinary object is an exotic object.

```js
var arr = [];
console.assert(arr[0] === arr['0']);
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
var values = Array.prototype.map.call(elems, obj => obj.value);

// Array.prototype.reduce(若提供了initialValue, currentIndex从0开始, 否则从1开始)
Array.prototype.reduce(function (accumulator, currentValue, currentIndex, array) {
  return accumulator + currentValue;
}, initialValue);

['a', 'b', 'c', 'a'].reduce((a, c) => {
  if (c in a) a[c]++;
  else a[c] = 1;

  return a;
}, {});

const flattenDeep = arr => Array.isArray(arr)
  ? arr.reduce((a, b) => [...flattenDeep(a), ...flattenDeep(b)], [])
  : [];

Array.prototype.sort(function (a, b) {
  return [-1, 0, 1][?];// SpiderMonkey can deal with true&false, v8 can not.
});

Array.prototype.slice(start, end);// [start, end)

// 若deleteCount被省略, 则默认被赋值为array.length - start
Array.prototype.splice(start, deleteCount, itemN);

[0, false, NaN, null, undefined,  '', 1].filter(v => v);

// range
Array.apply(null, {length: n}).map(Function.call, Number);
```

## ES2015+

```js
Array.of(1, 2, 3);
// polyfill
Array.of = () => Array.prototype.slice.call(arguments);

// range
Array.from({length: n}, (v, i) => i);
[...Array(n).keys()];
```

```js
var str1 = '123';
var str2 = [...str].reverse().join();
// desugar
var str2 = [].slice.call(str1).reverse().join();

const [...iterableObj] = [1, 2, 3];

let arr1 = [1, 2, 3];
let arr2 = [...arr1];
// desugar
var arr2 = [].concat(arr1); // equals to arr1.slice();

const [head, ...tail] = [1, 2, 3];

var arr1 = [1, 2];
var arr2 = [3, 4];
arr1.push(...arr2);
// desugar
Array.prototype.push.apply(arr1, arr2);
arr1.concat(arr2); // 不改变参与运算的数组

var nodeList = document.querySelectorAll('div');
var array = [...nodeList];

Array.prototype.fill(value, start, end);// [start, end)
```

## lodash

```js
const chunk = (arr ,size) => arr.reduce((a ,b, i, g) => !(i % size) ? a.concat([g.slice(i, i + size)]) : a, []);
const chunk = (arr, size) =>
  Array.from({length: Math.ceil(arr.length / size)}, (v, i) => arr.slice(i * size, i * size + size));

const compact = arr => arr.filter(Boolean);

const pull = (arr, ...args) => {
  let argState = Array.isArray(args[0]) ? args[0] : args;
  let pulled = arr.filter((v, i) => !argState.includes(v));
  arr.length = 0;
  pulled.forEach(v => arr.push(v));
};

const zipObject = (keys, values) => keys.reduce((obj, key, index) => (obj[key] = values[index], obj), {});
```
