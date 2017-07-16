####目录
- 数据属性和访问器属性
- 类数组对象
- let
#### 数据属性和访问器Accessor属性

数据属性:[[configurable]],[[enumerable]],[[writable]],[[value]]
访问器属性:[[configurable]],[[enumerable]],[[get]],[[set]]

	<h1 id="name">some text</h1>
	
	var person = {
		_age: 18,
		name: 'john'
	}
	Object.defineProperty(person, 'age', {
		get: function () {
			return document.getElementById('name').innerHTML;
		},
		set: function (newValue) {
			this._age = newValue;
			this.name += newValue;
			document.getElementById('name').innerHTML = newValue;
		}
		// enumerable: true,
  		// configurable: true
	})

注：属性后括号中表示默认值
***configurable***(false) :设置为 false，表示不能从对象中删除属性，如果对这个属性调用 delete，则在非严格模式下什么都不会发生，严格模式下报错,同时对象的value、writable、enumerable属性都无法修改（TypeError: Cannot redefine property）．需要注意的是全局声明变量时,对象的configurable为true
**enumerable**(true ???):存放一个布尔值，表示该属性是否可枚举，如果设为 false，会使得某些操作（比如 for-in 循环、Object.keys() 以及 **JSON.stringify**会跳过该属性）

#### 类数组对象
```
// 只存在整数属性的对象不存在length属性, 不为类数组对象
var a = { '1': 2 };

Array.prototype.slice.call(arguments);
```
#### let
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

#### ES2015+
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