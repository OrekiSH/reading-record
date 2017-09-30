## 继承

`Object.prototype`属性表示`Object`的原型对象, 所有的对象都继承了`Object.prototype`的属性和方法，它们可以被覆盖(除了以null为原型的对象，如 `Object.create(null)`),`Object.prototype`的`configurable`,`enumerable`,`writable`都为`false`
```js
console.assert(Object.create(null).constructor === undefined);
```

```js
console.assert(Object.prototype === Object.prototype);
// dont forget undefined === undefined
```

```js
console.assert(Object.__proto__ === Function.prototype);
console.assert(Function.prototype.__proto__ === Object.prototype);
console.assert(Object.prototype.__proto__ === null);

function ObjectCreate (obj, properties) {
  var F = function () {};
  F.prototype = obj;

  var createdObj = new F();
  if ({}.toString.call(properties) === '[object Object]')
    Object.defineProperties(createdObj, properties);

  return createdObj;
}
```

```js
var parent = {
  name: 'Jack',
  getSelf: function () {
    return this;
  },
};

var child = Object.create(parent, {
  age: {
    writable: true,
    configurable: true,
    value: 18,
  },
});
console.log(child); // more secret :)

var child = {
  ...parent,
  age: 18,
};
var child = Object.assign({}, parent, {
  age: 18,
});
console.log(child);
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
## statement and expression
```js
a = b * 2;
```
在ECMAScript中这样的等式被称为语句(statement), `a`,`b`被称为变量(变量被视为符号的容器)，`2`被称为字面值常量的值(literal value).

控制流(control flow), 声明(declarations), 函数与类，迭代器以及`import`,`export`都属于`statement`[statements](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements)

而这样一个语句是由多个表达式(expressions)组成的, 变量被称为变量表达式，字面值被称为字面值表达式, `b * 2`被称为算术表达式, 上述的整个声明被称为赋值(assignment)表达式.

高级语言最终还是要通过解释器(interpreter)或者编译器翻译成机器语言(机器语言是机器指令的集合，即一列二进制数字，计算机会将其转化为一列高低电平)计算机才能理解．[Understanding the differences: traditional interpreter, JIT compiler, JIT interpreter and AOT compiler](https://softwareengineering.stackexchange.com/questions/246094/understanding-the-differences-traditional-interpreter-jit-compiler-jit-interp/269878#269878)

## let and const
```js
console.log(foo);
var foo = 1;

console.log(foo); // ReferenceError
let foo = 1;
```
```js
a = 1;
var a;
console.log(a);

a = 1;
let a;
console.log(a);
```
与`var`声明不同, `let`声明和`const`声明不会被提升(hosit)到当前执行上下文的顶部

```js
var x = 'global';
let y = 'global';
console.log(this.x); // "global"
console.log(this.y); // undefined
```
`var`的作用域是函数作用域, `let`是块级作用域,定义的是块级变量,不在全局变量创建一个属性

```js
if (true) {
  let foo = 1;
}
console.log(foo); // ReferenceError

switch (1) {
  case 1:
    let name = 1;
  case 2:
    name = 2;
  console.log(name);
}

while (true) {
  let name = 1;
}

for (let key in { name: 1}) {}
console.log(key);

for (var i = 0; i < 10; i++)
  let name = i;
console.log(name, i);
```
变量从块作用域开始到let初始化位于"暂时性死区"中, 注意switch语句只存在一个作用块

```js
let foo = 1;
var foo = 2; // SyntaxError
```

```js
if (true) {
  let foo = 1;
}
console.log(foo);

// smart babel :)
if (true) {
  let _foo = 1;
}
console.log(foo);
```
```js
const { foo = 1 } = {
  foo: 2,
};
```

## ES2015+
```js
// stage3
var obj1 = { foo: 'bar', x: 42 };
var obj2 = { foo: 'baz', y: 13 };

var clonedObj = { ...obj1 };
var mergedObj = { ...obj1, ...obj2 };

const PI = 3.141593;
// desugar
Object.defineProperty(typeof  global === 'object' ? global : window, 'PI', {})
```
