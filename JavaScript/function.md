```js
const toString = Object.prototype.toString;
```
## prototype

```js
function fn () {}

// "constructor" property only
Object.getOwnPropertyNames(fn.prototype);
// nine properties
Object.getOwnPropertyNames(fn.__proto__);
// has "__proto__" property
Object.getOwnPropertyNames(fn.prototype.__proto__);
console.assert(fn.prototype.__proto__ === fn.__proto__.__proto__);

Object.getOwnPropertyDescriptor(fn.prototype, 'constructor');
Object.getOwnPropertyDescriptor(fn.__proto__, 'constructor');
```

```js
var f = new fn()
console.assert(f.__proto__ === fn.prototype);
```

```js
console.assert(Function.prototype === Function.prototype);
console.assert(Function.prototype === Function.__proto__);
```
## execution context

The unique global object is created before control enters any execution context.

When control is transferred to ECMAScript **executable code**(global code, eval code, function code), control is entering an execution context.

Active execution contexts logically form a stack. The top execution context on this logical stack is the running execution context.

initial global execution context
- Set the VariableEnvironment to the Global Environment.
- Set the LexicalEnvironment to the Global Environment.
- Set the ThisBinding to the global object.

```js
// var foo = 'bar';
declarations: [{
  type: 'VariableDeclarator',
  id: { type: 'Identifier', name: 'foo' },
  init: { type: 'Literal', value: 'bar' },
}]
```

```js
var foo = 'bar';
function foo () {};
console.log(foo);
```

```js
console.log(foo);
var foo = 'bar';
function foo () {}
```

```js
foo();
function foo () { console.log(1); }
var foo = function () { console.log(2); }
```

## lexical scope (static scope)

```js
function foo () { console.log(scope); }
var scope = 'global';
foo();
```

```js
function foo () { console.log(a); }
function bar () {
  var a = 2;
  foo();
}

var a = 1;
bar();
```

```js
var scope = 'global';

function checkScope () {
  function fn () { console.log(scope); }
  var scope = 'local';

  return fn;
}

var fn = checkScope();
fn();
checkScope()();
```

```js
/*
var fooReference = {
  base: EnvironmentRecord,
  name: 'foo',
  strict: false,
};
*/
function foo () { return this; }
foo();
```

```js
let a = {
  name: 'a',
  fn: function () { return this; },
};
console.assert(a.fn() === a);
console.assert((a.fn)() === a);
console.log((false || a.fn)());
```

```js
let fn = function () { return this; }
let a = {
  name: 'a',
  fn,
};
console.log(a.fn());
```

```js
let f = function () {
  let name = 'fn';
  return function () {
    return this;
  };
}
let a = {
  name: 'a',
  fn: f,
};

let foo = a.fn();
let bar = foo;
console.assert(foo() === bar());
```
```js
function captureShadow (shadowed) {
  return function (shadowed) {
    return shadowed;
  };
}

let captured = captureShadow(1);
console.log(captured(2));
```

```js
var Constructor = function () {
  function Constructor () {
    console.log(this);
  }

  return Constructor;
};
```

## closure

```js
const obj = (function () {
  let data = {};
  return (key, val) => {
    if (val === undefined) return data[key];
    return data[key] = val;
  };
})();
```

```js
let theThing = {};
setInterval(() => {
  const originalThing = theThing;

  const unused = function () {
    if (originalThing)
      console.log('hi');
  };

  theThing = {
    longStr: new Array(1e7).join('*'),
    someMethod: function () {},
  };
}, 100);
```

```js
const stopWhenN = (function (n, delay, callback) {
  let count = 0;
  let timer = null;

  return function stopWhenN(n, delay, callback) {
    if (count >= n) {
      clearTimeout(timer);
      return;
    };

    count += 1;
    typeof callback === 'function' ? callback() : (function () {})();

    timer = setTimeout(function () {
      stopWhenN(n, delay, callback);
    }, delay);
  }
})();
```


## strict mode
*IE在IE10引入

①下面的情况都会在脚本运行之前抛出**SyntaxError**异常:

- 八进制语法: `var n = 023`和`var s = '\047'`
- with语句
- 使用delete删除变量名 `var x; delete x;`
- 使用arguments,eval或implements等未来保留字作为变量或函数名
- 在语句块中使用函数声明
- 对象字面量中使用相同属性名
- 函数形参中使用相同参数名

②下面的情况都会在脚本运行之前抛出**TypeError**异常

- 静默失败(silently fail)的赋值操作(不可写属性,只读属性,不可扩展对象属性)
- 删除一个不可配置的属性 `delete Object.prototype`
- 访问arguments.**callee**, arguments.**caller**, anyFunction.caller以及anyFunction.arguments

③给未声明的变量赋值会抛出**ReferenceError**异常

④函数调用中的this会指向**undefined**,当函数通过call,apply,bind调用时,this的值为**传入参数的值**,不进行类型转换


## 创建对象(with return)

```js
function Parent (name) {
  var obj = {};
  obj.name = name;

  return obj;
}
// 工厂(factory)模式
var personA = Parent('Jack');
// 寄生构造函数(parasitic constructor)模式
var personB = new Parent('Jack');
console.assert(personA.constructor === personB.constructor);
```

```js
function Parent (name) {
  var obj = function () {};
  obj.prototype.name = name;
  obj.prototype.constructor = this;

  return new obj();
}

var personA = Parent('Jack');
var personB = new Parent('Jack');
console.assert(personA.constructor !== personB.constructor);
```

## 创建对象(without return)

```js
function Parent (name) {
  this.name = name;
  // equals to this.getSelf = new Function ('return this;');
  this.getSelf = function () { return this; };
}
var person = new Parent('Jack');
// set property 'name' of global
// var person = Parent('Jack');
```

```js
function Parent () {}
Parent.prototype.friends = [];

var A = new Parent();
A.friends.push({ name: 'C' });
var B = new Parent();
B.friends.push({ name: 'D' });
// B.__proto__.friends = [];
```

```js
function Parent () {
  this.friends = [];
}
// 组合(combination)模式
Parent.prototype.name = 'Jack';
var A = new Parent();
var B = new Parent();

A.friends.push({ name: 'C' });
```

```js
function Parent (name) {
  this.name = name;
  // 动态原型(dynamic prototype)模式
  if ({}.toString.call(this.getSelf) !== ['object Function']) {
    Parent.prototype.getSelf = function () { return this; };
  }
}
```

```js
function Parent () {}
Parent.prototype = undefined;
var person = new Parent();
console.assert(person.__proto__ === Object.prototype);

Parent.prototype = Object.create(null);
var person = new Parent();
console.assert(person.__proto__ === undefined);
```

```js
function defineProperty (obj, key, value) {
  Object.defineProperty(obj, key, {
    value,
    writable: true,
    configable: true,
  });
}

function Parent (data) {
  defineProperty(this, 'data', data);
  defineProperty(this, 'status', 'pending');
}

defineProperty(Parent.prototype, 'getSelf', function () {
  return this;
});

defineProperty(Parent.prototype, Symbol.toStringTag, 'Parent');

var person = new Parent({
  name: 'Jack',
});

console.log(Object.getOwnPropertyNames(Parent.prototype));
console.log(Object.getOwnPropertySymbols(Parent.prototype));
```

## 继承
```js
// 组合继承
function Parent (lastName, firstName = 'Nicholas', friends) {
  console.log(Parent.prototype);
  this.name = `${firstName} ${lastName}`;
  this.friends = friends;
}

Parent.prototype.say = function () { return this.name; };
Parent.prototype.callFriends = function () {
  this.friends.forEach(e => console.log(e));
};
Parent.prototype.addFriend = function (e) {
  this.friends.push(e);
}

function Children (lastName, friends) {
  Parent.apply(this, [lastName, undefined, friends]);
}

Children.prototype = new Parent();
Children.prototype.constructor = Parent;

var father = new Parent('C.Zakas', undefined, [1, 2, 3]);
console.log(father.say());
father.addFriend(4);
father.callFriends();

var son = new Children('Zakas',[1, 2]);
console.log(son.say());
son.callFriends();
```

```js
// 原型链
function SuperType () {
  this.property = true;
}
SuperType.prototype.getSuperTypeValue = function () {
  return this.property;
};

function SubType () {
  this.subproperty = false;
}

SubType.prototype = new SuperType();
SubType.prototype.getSubTypeValue = function () {
  return this.subproperty;
};

var instance = new SubType();

// node v6.10.2: SuperType { subproperty: false }
// chrome v58.0: SubType { subproperty: false }
// firefox v54.0: Object { subproperty: false }
console.log(instance);
console.assert(instance.getSuperTypeValue() === true);
console.assert(instance.constructor === SuperType.prototype);
```

## ES2015+
```js
(param1, param2, …, paramN) => expression;
(param1, param2, …, paramN) => { return expression; }

_ => { statements; }
() => { statements; }

(param1, param2, ...rest) => { statements }
```

```js
function fn (x, y, z) { }
var args = [0, 1, 2];

fn(...args);

var Foo = () => {};
var foo = new Foo(); // TypeError
console.assert(Foo.prototype === undefined);

var fn = () => { return this; }
// desugar to
'use strict';
var fn = function fn () {
  return undefined;
};

// arguments
var f = () => [...arguments];
console.log(f(1,2,3)); // ReferenceError
```

```js
class Counter {
  static state = {
    name: 'counter',
  };

  constructor () { this.id = 1; }
  add () { this.id++; }
}

// desugar
var _createClass = function () {
  function defineProperties (target, props) {
    for (var i = 0; i < props.length; i++) {
      var descriptor = props[i];
      descriptor.enumerable = descriptor.enumerable || false;
      descriptor.configurable = true;
      if ("value" in descriptor) 
        descriptor.writable = true;
        
      Object.defineProperty(target, descriptor.key, descriptor);
    }
  }
  
  return function (Constructor, protoProps, staticProps) {
    if (protoProps) 
      defineProperties(Constructor.prototype, protoProps);
    if (staticProps) 
      defineProperties(Constructor, staticProps);
    return Constructor;
  };
}();

function _classCallCheck (instance, Constructor) {
  if (!(instance instanceof Constructor)) {
    throw new TypeError("Cannot call a class as a function");
  }
}

var Counter = function () {
  function Counter () {
    _classCallCheck(this, Counter);

    this.id = 1;
  }

  _createClass(Counter, [
    {
      key: 'add',
      value: function add() {
        this.id++;
      },
    },
  ]);

  return Counter;
}();

Counter.state = {
  name: 'counter',
};
```

## 函数柯里化 currying
```js
var add = function(x) {
  return function(y) {
    return x + y;
  };
};

console.assert(add(1)(2) === 3);
```
## 垃圾回收

- 函数中的局部变量只在函数运行时存在,在函数执行结束后应该释放其内存
- 标记清除 mark-and-swap: 当变量进入函数(如在函数中声明变量),就将变量标记为"进入环境"
- 引用计数: 将一个引用类型值赋给变量,该值的引用次数加1.引用计数会导致循环引用.
