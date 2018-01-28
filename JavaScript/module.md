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
> define(id?, dependencies?, factory);

依赖参数是可选的，如果忽略此参数，它应该默认为["require", "exports", "module"].无论函数体中是否用到了`require`,模块 factory 构造方法的第一个参数必须命名,且必须为`require`.
```js
// RequireJS
deps = (callback.length === 1 ? ['require'] : ['require', 'exports', 'module']).concat(deps);
```

```html
<script data-main="" src="https://cdn.bootcss.com/require.js/2.3.3/require.js"></script>
```

```js
// 模块系统的启动(relative)
require(['./mod'], function (mod) { });

// top-level
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
define(function (require, exports, module) {
  // 在加载完成时，执行回调
  require.async(['./c', './d'], function(c, d) {
    c.doSomething();
    d.doSomething();
  });
  // 解析后的模块绝对路径
  console.log(require.resolve('./b'));
  // 当前模块的绝对路径
  console.log(module.uri);
});
```

## UMD
```js
// nodeAdapter.js
(function (define) {
  define(function (require, exports, module) {
    var b = require('b');

    return function () {};
  });
}( // Help Node out by setting up define.
    typeof module === 'object' && module.exports && typeof define !== 'function' ?
    function (factory) { module.exports = factory(require, exports, module); } :
    define
));
```

```js
// commonjsAdapter.js 
if (typeof exports === 'object' && typeof exports.nodeName !== 'string' && typeof define !== 'function') {
  var define = function (factory) {
    factory(require, exports, module);
  };
}

define(function (require, exports, module) {
  var b = require('b');

  exports.action = function () {};
});
```

```js
;(function (root, factory) {
if(typeof exports === 'object' && typeof module === 'object')
  module.exports = factory()
else if(typeof define === 'function' && define.amd)
  define([], factory)
else if(typeof exports === 'object')
  exports['module-name'] = factory()
else
  root['module-name] = factory()
})(this, function () {});
```

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
```js
import defaultExport from "module-name";
import * as name from "module-name";
import { export } from "module-name";
import { export as alias } from "module-name";
import { export1 , export2 } from "module-name";
import { export1 , export2 as alias2 , [...] } from "module-name";
import defaultExport, { export [ , [...] ] } from "module-name";
import defaultExport, * as name from "module-name";
import "module-name";

export { name1, name2, …, nameN };
export { variable1 as name1, variable2 as name2, …, nameN };
export let name1, name2, …, nameN; // also var, function
export let name1 = …, name2 = …, …, nameN; // also var, const

export default expression;
export default function (…) { … } // also class, function*
export default function name1 (…) { … } // also class, function*
export { name1 as default, … };

export * from …;
export { name1, name2, …, nameN } from …;
export { import1 as name1, import2 as name2, …, nameN } from …;
```

## webpack

```js
import chunk from 'lodash/chunk';

class Main {
  constructor() {
    console.log(this, chunk);
  }
}
const m = new Main();

export default Main;
```

```js
(function (modules) {
  var installedModules = {};

  function __webpack_require__ (moduleId) {
    if (installedModules[moduleId]) {
      return installedModules[moduleId].exports;
    }

    var module = installedModules[moduleId] = {
      i: moduleId,
      l: false,
      exports: {},
    };
    // Execute the module function
    modules[moduleId].call(module.exports, module, module.exports, __webpack_require__);
    module.l = true;

    return module.exports;
  }

  __webpack_require__.m = modules;

  __webpack_require__.c = installedModules;

  __webpack_require__.o = function (object, property) { return Object.prototype.hasOwnProperty.call(object, property); };
  // define getter function for harmony exports
  __webpack_require__.d = function (exports, name, getter) {
    if(!__webpack_require__.o(exports, name)) {
  		Object.defineProperty(exports, name, {
  			configurable: false,
  			enumerable: true,
  			get: getter,
  		});
  	}
  };
  // getDefaultExport function for compatibility with non-harmony modules
  __webpack_require__.n = function (module) {
  	var getter = module && module.__esModule ?
  		function getDefault() { return module['default']; } :
  		function getModuleExports() { return module; };
  	__webpack_require__.d(getter, 'a', getter);
  	return getter;
  };
  // __webpack_public_path__
  __webpack_require__.p = "";

  return __webpack_require__(__webpack_require__.s = 3);
})
([
  (function (module, exports) {
    module.exports = isObject; // 0
  }),
  (function (module, exports, __webpack_require__) {
    module.exports = baseGetTag; // 1
  }),
  (function (module, exports, __webpack_require__) {
    module.exports = Symbol; // 2
  }),
  (function (module, __webpack_exports__, __webpack_require__) {
    "use strict";
    Object.defineProperty(__webpack_exports__, "__esModule", { value: true });
    /* harmony import */ var __WEBPACK_IMPORTED_MODULE_0_lodash_chunk__ = __webpack_require__(4);
    /* harmony import */ var __WEBPACK_IMPORTED_MODULE_0_lodash_chunk___default = __webpack_require__.n(__WEBPACK_IMPORTED_MODULE_0_lodash_chunk__);

    class Main {
      constructor() {
        console.log(this, __WEBPACK_IMPORTED_MODULE_0_lodash_chunk___default.a);
      }
    }
    const m = new Main();

    /* harmony default export */ __webpack_exports__["default"] = (Main);
  }),
  (function (module, exports, __webpack_require__) {
    module.exports = chunk; // 4
  })
])
```
