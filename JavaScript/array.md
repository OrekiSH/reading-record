#### 目录

#### 特性
数组是一段线性分配的内存，它通过整数计算偏移并访问其中的元素，JavaScript**没有**像此类数组一样的数据结构．

JavaScript中的数组是一种拥有一些类数组特性的**对象**，其第一个值将获得**整数属性名'0'**

只存在整数属性的对象不存在length属性,只存在非整数属性的数组length为0．数组下标的表达式最终被转换为**字符串**

	var a = [];
	a['a'] = 1; // a.length = 0
	a['2'] = 2; //a.length = 3
	a.length = 0
	console.log(a)

#### Array.prototype
```
var elems = document.querySelectorAll('select option:checked');
var values = Array.prototype.map.call(elems, function(obj) {
  return obj.value;
});

var str = '12345';
var str1 = [...str].reverse().join();
// desugar
var str2 = [].slice.call(str).reverse().join();
```

#### 拓展运算符

```
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

