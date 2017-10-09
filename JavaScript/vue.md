```js
function Vue (options) {
  this.$options = options || {};

  var data = this._data = this.$options.data;

  Object.keys(data).forEach(key => {
    this._proxyData(key);
  });

  observe(data);

  // this.$compile = new Compile(options.el || document.body, this);
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
