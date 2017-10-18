```js
function Vue (options) {
  this.$options = options || {};

  var data = this._data = this.$options.data;

  Object.keys(data).forEach(key => {
    this._proxyData(key);
  });

  observe(data);

  this.$compile = new Compile(options.el || document.body, this);
}

Vue.prototype._proxyData = function (key) {
  var vm = this;

  Object.defineProperty(vm, key, {
    configurable: true,
    enumerable: true,
    get () {
      return vm._data[key];
    },
    set (newVal) {
      vm._data[key] = newVal;
    },
  });
};
```

```js
function observe (value) {
  if (!value || typeof value !== 'object') return;

  return new Observer(value);
}

function Observer (data) {
  this.walk(data);
}

Observer.prototype = {
  walk (data) {
    Object.keys(data).forEach(key => {
      this.defineReactive(data, key, data[key]);
    });
  },
  defineReactive (data, key, val) {
    var dep = new Dep();
    var child = observe(val);

    Object.defineProperty(data, key, {
      enumerable: true,
      configurable: true,
      get: function () {
        if (Dep.target) dep.depend();

        return val;
      },
      set: function (newVal) {
        if (val === newVal) return;

        val = newVal;
        child = observe(newVal);
        dep.notify();
      },
    });
  },
};

var uid = 0;
function Dep () {
  this.id = uid++;
  this.subs = [];
}

Dep.prototype = {
  addSub (sub) {
    this.subs.push(sub);
  },
  depend () {
    Dep.target.addDep(this);
  },
  notify () {
    this.subs.forEach(sub => {
      sub.update();
    });
  },
};

// watcher
Dep.target = null;

function Watcher (vm, exp, cb) {
  this.cb = cb;
  this.vm = vm;
  this.exp = exp;
  this.depIds = {};
  this.value = this.get();
}

Watcher.prototype = {
  update () {
    var value = this.get();
    var oldVal = this.value;

    if (value !== oldVal) {
      this.value = value;
      this.cb.call(this.vm, value, oldVal);
    }
  },
  get () {
    Dep.target = this;
    var value = this.getVMVal();
    Dep.target = null;

    return value;
  },
  getVMVal () {
    var exp = this.exp.split('.');
    var value = this.vm._data;
    exp.forEach(key => {
      value = value[key];
    });

    return value;
  },
  addDep (dep) {
    if (this.depIds.hasOwnProperty(dep.id)) return;

    dep.addSub(this);
    this.depIds[dep.id] = dep;
  },
};
```

```js
function Compile (el, vm) {
  this.$vm = vm;
  this.$el = this.isElementNode(el) ? el : document.querySelector(el);
  this.$fragment = this.node2Fragment(this.$el);

  this.init();

  this.$el.appendChild(this.$fragment);
}

Compile.prototype = {
  node2Fragment (el) {
    const fragment = document.createDocumentFragment();
    let child = null;

    // stop when child is null
    while (child = el.firstChild) {
      fragment.appendChild(child);
    }

    return fragment;
  },

  init () {
    this.compileElement(this.$fragment);
  },

  compileElement (el) {
    const childNodes = el.childNodes;
    const me = this;

    [].slice.call(childNodes).forEach(node => {
      const text = node.textContent;
      const regex = /\{\{(.*)\}\}/;

      if (me.isElementNode(node)) {
        me.compile(node);
      } else if (me.isTextNode(node) && regex.test(text)) {
        me.compileText(node, RegExp.$1);
      }

      if (node.childNodes && node.childNodes.length) {
				me.compileElement(node);
			}
    });
  },

  compile (node) {
		const nodeAttrs = node.attributes;
		const me = this;

		[].slice.call(nodeAttrs).forEach(attr => {
			const attrName = attr.name;
			if (me.isDirective(attrName)) {
				const exp = attr.value;
				const dir = attrName.substring(2);

				if (me.isEventDirective(dir)) {
					compileUtil.eventHandler(node, me.$vm, exp, dir);
				} else {
					compileUtil[dir] && compileUtil[dir](node, me.$vm, exp);
				}

				node.removeAttribute(attrName);
			}
		});
	},

	compileText (node, exp) {
		compileUtil.text(node, this.$vm, exp);
	},

	isDirective (attr) {
		return attr.indexOf('v-') == 0;
	},

  isEventDirective (dir) {
    return dir.indexOf('on') === 0;
  },

	isElementNode (node) {
		return node.nodeType == 1;
	},

	isTextNode (node) {
		return node.nodeType == 3;
	},
};

const compileUtil = {
	text (node, vm, exp) {
		this.bind(node, vm, exp, 'text');
	},

	model (node, vm, exp) {
		this.bind(node, vm, exp, 'model');

		const me = this;
    let val = this._getVMVal(vm, exp);
		node.addEventListener('input', e => {
			const newValue = e.target.value;
			if (val === newValue) {
				return;
			}

			me._setVMVal(vm, exp, newValue);
			val = newValue;
		});
	},

	bind (node, vm, exp, dir) {
		const updaterFn = updater[dir + 'Updater'];

		updaterFn && updaterFn(node, this._getVMVal(vm, exp));

		new Watcher(vm, exp, (value, oldValue) => {
			updaterFn && updaterFn(node, value, oldValue);
		});
	},

	eventHandler (node, vm, exp, dir) {
		const eventType = dir.split(':')[1];
    const fn = vm.$options.methods && vm.$options.methods[exp];

		if (eventType && fn) {
  		node.addEventListener(eventType, fn.bind(vm), false);
    }
	},

	_getVMVal: function(vm, exp) {
		let val = vm._data;
		exp = exp.split('.');
		exp.forEach(k => {
			val = val[k];
		});
		return val;
	},

	_setVMVal: function(vm, exp, value) {
		let val = vm._data;
		exp = exp.split('.');
		exp.forEach((k, i) => {
			if (i < exp.length - 1) {
				val = val[k];
			} else {
				val[k] = value;
			}
		});
	}
};

const updater = {
	textUpdater (node, value) {
		node.textContent = typeof value == 'undefined' ? '' : value;
	},

	modelUpdater (node, value, oldValue) {
		node.value = typeof value == 'undefined' ? '' : value;
	},
};
```

```html
<div id="app">
  <h1>{{count}}</h1>
  <button v-on:click="add">add</button>

  <h2>{{msg}}</h2>
  <!--something wrong with 汉字 input-->
  <input type="text" v-model="msg">
</div>

<script>
  var vm = new Vue({
    el: '#app',
    data: {
      count: 0,
      msg: '',
    },
    methods: {
      add: function () {
        this.count += 1;
      },
    },
  });
</script>
```
