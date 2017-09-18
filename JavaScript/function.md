## scope
JavaScript采用的是词法作用域(Lexical Scope)

```js
function foo() {
  console.log(a);
}

function bar() {
  var a = 2;
  foo();
}

var a = 1;
bar();
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
```
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

## 创建对象
```js
//工厂模式
function Person (name, age) {
  var obj = {};
  obj.name = name;
  obj.age = age;

  return obj;
}
var p = Person('L', 18);

console.assert(!(p instanceof Person));
console.assert(p.constructor === Object);
```

```js
//构造函数(构造函数内定义的函数要在每个实例上重新创建)
function Person (name, age) {
  this.name = name;
  this.age = age;
}

var p = new Person('L', 18);

// 对象的__proto__属性指向对象constructor属性的原型
console.assert(Object.constructor === Function);
console.assert(Function.constructor === Function);

console.assert(p.constructor === Person);

console.assert(Person.prototype.constructor === Person);

// p的__proto__属性指向Person的原型
console.assert(p.__proto__ === Person.prototype)
// Person的原型的__proto__指向Object的原型
console.assert(Person.prototype.__proto__ === Object.prototype);
console.assert(Object.prototype.__proto__ === null);

console.assert(Person.__proto__ === Function.prototype);
console.assert(Object.__proto__ === Function.prototype);
console.assert(Function.prototype.__proto__ === Object.prototype);
```

```js
//原型(修改原型中包含引用类型值的属性导致的共享问题)
function Person () {};

Person.prototype = {
  type: 'human being',
  getType:  function () {
    return this.type;
  },
  // constructor: Person,(重设constructor属性,[[enumerable]]将被设置为true, 也可用defineProperty重设constructor属性)
};

var p = new Person();

console.assert(!p.hasOwnProperty('type'));
```

```js
// 组合模式
function Person (name) {
  this.name = name;
  this.friends = ['A', 'K'];
}

Person.prototype = {
  constructor: Person,
  getName: function () {
    console.log(this.name);
  }
}

var l = new Person('L');
var n = new Person('N');
l.friends.push('M');
console.assert(l.friends !== n.friends);
console.assert(l.getName === l.__proto__.getName);
console.assert(l.getName === Person.prototype.getName);

console.assert(Person.getName === undefined);
```

## 继承
```js
// 组合继承
function Parent (lastName, firstName = 'Nicholas', friends) {
  console.log(Parent.prototype);
  this.name = `${firstName} ${lastName}`;
  this.friends = friends;
}

Parent.prototype.say = function () {
  return this.name;
};

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

```
SubType.prototype = {
  constructor: SuperType,
  getSubTypeValue () {
    return this.subproperty;
  }
};
console.log(instance);
{
  subproperty: false,
  __proto__: Object {
    constructor: function SuperType()
    getSubTypeValue: function getSubTypeValue()
    __proto__: Object {}
  }
}
```
```
SubType.prototype = {
  constructor: new SuperType(),
  getSubTypeValue () {
    return this.subproperty;
  }
};
console.log(instance);
{
  subproperty: false,
  __proto__: Object {
    constructor: SuperType {
      property: true
      __proto__: Object {
        constructor: function SuperType()
        getSuperTypeValue: function getSuperTypeValue()
        __proto__: Object {}
      }
    }
    getSubTypeValue: function getSubTypeValue()
    __proto__: Object {}
  }
}
```
```
SubType.prototype = {
  constructor: SuperType.prototype,
  getSubTypeValue () {
    return this.subproperty;
  }
};
console.log(instance);
{
  subproperty: false,
  __proto__: Object {
    constructor: Object {
      constructor: function SuperType()
      getSuperTypeValue: function getSuperTypeValue()
      __proto__: Object {}
    }
    getSubTypeValue: function getSubTypeValue()
    __proto__: Object {}
  }
}
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
function myFunction(x, y, z) { }
var args = [0, 1, 2];

myFunction(...args);

var Foo = () => {};
var foo = new Foo(); // TypeError
console.assert(Foo.prototype === undefined)

// arguments
var f = () => [...arguments];
console.log(f(1,2,3)); // ReferenceError

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
