## 前端模块化开发的价值
```js
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

## AMD
```js
define(id?, dependencies?, factory);
```
依赖参数是可选的，如果忽略此参数，它应该默认为["require", "exports", "module"].无论函数体中是否用到了`require`,模块 factory 构造方法的第一个参数必须命名,且必须为`require`.

```js
<script data-main="src/main" src="https://cdn.bootcss.com/require.js/2.3.3/require.js"></script>
// 模块系统的启动
require(['./mod'], function (mod) { });

// load jQuery
require.config({
  paths: {
    jquery: 'https://cdn.bootcss.com/jquery/3.2.1/jquery',
    // jquery: '../node_modules/jquery/dist/jquery',
  },
});
require(['jquery'], function ($) { });

// config in config.js without any exports
require(['./config'], function() {
    // Configuration loaded now, safe to do other require calls that depend on that config.
    require(['jquery'], function($) { });
});
```

```js
 exports.action = function () { }
 return {
   action: function () { }
 }
 module.exports = {
   action: function () { }
 }
 define({
   action: function () { }
 })

 // wrong
 exports = {
   action: function () { }
 }
```

```js
(function (global) {
  function require (deps, callback) {}
  function define (id, deps, factory) {}
  
  function createScript (name, onload) {
    var script = document.createElement('script');
    script.type = 'text/javascript';
    script.charset = 'utf-8';
    script.async = true;
    script.setAttribute('data-module', name);

    function innerOnLoad() {
      if (!script.readyState || /^complete$|^loaded$/.test(script.readyState)) {
        script.onreadystatechange = script.onload = null;
      }

      onload && onload();
    }

    if (script.readyState) {
      script.onreadystatechange = innerOnLoad;
    } else {
      script.onload = innerOnLoad;
    }

    script.src = /.+\.js$/i.test(name) ? name : name + '.js';
    document.getElementsByTagName('head')[0].appendChild(script);
  }
  
  function getCurrentScript () {
    if (document.currentScript) {
      return document.currentScript;
    }

    var scripts = document.getElementsByTagName('script');
    var len = scripts.length;
    while (len--) {
      var script = scripts[len];
      if (script.readyState === 'interactive') {
        return script;
      }
    }
  }
  
  define.amd = {};
  global.define = define;
  global.require = require;
}(this));
```

## CMD
```js
define(factory);
// define(id?, dependencies?, factory);
```
无论函数体中是否用到了`require`,模块 factory 构造方法的第一个参数必须命名,且必须为`require`.

`require`的参数值必须是字符串直接量
```js
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
```js
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
`exports`是`module.exports`的一个引用

## UMD

## CommonJS
```js
const _ = require('lodash');
Module {
  id: '.',
  exports: {},
  parent: null,
  filename: '当前模块的绝对路径',
  loaded: false,
  children:
   [ Module {
       id: 'node_modules/lodash/lodash.js的绝对路径',
       exports: [Object],
       parent: [Circular],
       filename: 'node_modules/lodash/lodash.js的绝对路径',
       loaded: true,
       children: [],
       paths: [Array] } ],
  paths:
   [ 'node_modules的绝对路径',
     ......(各级node_module目录)
     '/node_modules' ]
}

module.exports = {};
module.exports.noop = function () {};
exports.noop = function () {};
// wrong
exports = {};
console.assert(module.exports === exports);
// Module { load, require, _compile }
console.log(module.__proto__);

function require(/* ... */) {
  const module = { exports: {} };
  ((module, exports) => {
    // Module code here. In this example, define a function.
    function someFunc() {}
    exports = someFunc;
    // At this point, exports is no longer a shortcut to module.exports, and
    // this module will still export an empty default object.
    module.exports = someFunc;
    // At this point, the module will now export someFunc, instead of the
    // default object.
  })(module, module.exports);
  return module.exports;
}

Module.prototype.require = function (path) {
  return Module._load(path, this);
};

// 删除所有模块的缓存
Object.keys(require.cache).forEach(function(key) {
  delete require.cache[key];
})


// module-a.js
const b = require('./module-b');
const a = 'a';
console.log({ name: 'module-a', a, b });

// module-b.js
const a = require('./module-a');
const b = 'b';
console.log({ name: 'module-b', a, b });

// index.js
// { name: 'module-b', a: {}, b: 'b' }
// { name: 'module-a', a: 'a', b: 'b' }
// When index.js loads a.js, then a.js in turn loads b.js. At that point, b.js tries to load a.js. In order to prevent an infinite loop, an unfinished copy of the a.js exports object is returned to the b.js module. b.js then finishes loading, and its exports object is provided to the a.js module.
const a = require('./module-a');
const b = require('./module-b');
```

## ES6 Modules
