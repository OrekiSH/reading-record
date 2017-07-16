### 前端模块化开发的价值
```
// 命名冲突
function func () {}
function func () {}

// 解决命名冲突: 引入namespace
var org = {};
org.cometd = {};
org.cometd.Utils = {};
org.cometd.Utils.func = function () {}

// 文件依赖
dialog.js依赖于utils.js,但是utils.js未引入.
```
---

### AMD
```
define(id?, dependencies?, factory);
```
依赖参数是可选的，如果忽略此参数，它应该默认为["require", "exports", "module"].无论函数体中是否用到了`require`,模块 factory 构造方法的第一个参数必须命名,且必须为`require`.

```
<script data-main="src/main" src="https://cdn.bootcss.com/require.js/2.3.3/require.js"></script>
// 模块系统的启动
require(['./mod'], function (mod) { });

require.config({
	paths: {
	  jquery: 'https://cdn.bootcss.com/jquery/3.2.1/jquery',
	},
});

require(['jquery', './main'], function ($, main) { });

require(['scripts/config'], function() {
    // Configuration loaded now, safe to do other require calls that depend on that config.
    require(['foo'], function(foo) { });
});
```

```
// 正确写法1
 exports.action = function () { }
 // 正确写法2
 return {
 	action: function () { }
 }
 // 正确写法3
 module.exports = {
 	action: function () { }
 }
 // 正确写法4
 define({
 	action: function () { }
 })
 
 // 错误写法
 exports = {
 	action: function () { }
 }
```
---
### CMD
```
define(factory);
// define(id?, dependencies?, factory);
```
无论函数体中是否用到了`require`,模块 factory 构造方法的第一个参数必须命名,且必须为`require`.

`require`的参数值必须是字符串直接量
```
// 模块系统的启动
seajs.use('./path/mod', function (mod) { });

seajs.config({
	alias: {
		/*
		需要将jquery.js封装
		 define(function(){
		    //jquery源代码
		    return $.noConflict();
		});
		*/
		jquery: './path/jquery.js',
	},
})
seajs.use('./main');

```
```
define(function(require, exports, module) {

  // 异步加载一个模块，在加载完成时，执行回调
  require.async('./b', function(b) {
    b.doSomething();
  });

  // 异步加载多个模块，在加载完成时，执行回调
  require.async(['./c', './d'], function(c, d) {
    c.doSomething();
    d.doSomething();
  });
  
  // 解析后的模块绝对路径
  console.log(require.resolve('./b'));
  
  // 当前模块的绝对路径
  console.log(module.uri);
  
  // 当前模块的依赖
  console.log(module.dependencies);
});
```
```
// 正确写法1
 exports.action = function () { }
 // 正确写法2
 return {
 	action: function () { }
 }
 // 正确写法3
 module.exports = {
 	action: function () { }
 }
 // 正确写法4
 define({
 	action: function () { }
 })
 
 // 错误写法
 exports = {
 	action: function () { }
 }
```
`exports`是`module.exports`的一个引用

---
### UMD

---
### CommonJS

---
### ES6 Modules
