## 继承

```js
var a = {
  x: 10,
  calculate: function (z) {
    return this.x + this.y + z;
  },
};

// 原型
var b = {
  y: 20,
  __proto__: a,
};

var c = {
  y: 30,
  __proto__: a,
};

//　拼接
var b = Object.assign({}, a, {
  y: 20,
});

var c = Object.assign({}, a, {
  y: 30,
});

b.calculate(30); //60
c.calculate(40); //80
```
## 数据属性和访问器Accessor属性
```js
// 数据属性
[[configurable]],
[[enumerable]],//for-in,Object.keys(),JSON.stringify()
[[writable]],
[[value]]
// 访问器属性
[[configurable]],
[[enumerable]],
[[get]],
[[set]]
```
```js
<h1 id="name">some text</h1>

var person = {
  _age: 18,
  name: 'john'
};

Object.defineProperty(person, 'age', {
  get: function () {
    return document.getElementById('name').innerHTML;
  },
  set: function (newValue) {
    this._age = newValue;
    this.name += newValue;
    document.getElementById('name').innerHTML = newValue;
  },
});
```

## 类数组对象
```js
// 只存在整数属性的对象不存在length属性, 不为类数组对象
var a = { '1': 2 };

Array.prototype.slice.call(arguments);
```
## let
```
// let声明不会被提升到当前执行上下文的顶部
// 变量从块作用域开始到let初始化位于"暂时性死区"中(switch语句只存在一个作用块)
console.log(foo); // ReferenceError
let foo = 1;

// var的作用域是函数作用域
// let是块级作用域,定义的是块级变量
// 不在全局变量创建一个属性
var x = 'global';
let y = 'global';
console.log(this.x); // "global"
console.log(this.y); // undefined

let foo = 1;
var foo = 2; // SyntaxError
```

## ES2015+
```
// stage3
var obj1 = { foo: 'bar', x: 42 };
var obj2 = { foo: 'baz', y: 13 };

var clonedObj = { ...obj1 };
var mergedObj = { ...obj1, ...obj2 };

const PI = 3.141593;
// desugar
Object.defineProperty(typeof  global === 'object' ? global : window, 'PI', {})
```
