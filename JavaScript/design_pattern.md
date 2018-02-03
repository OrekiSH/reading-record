## 观察者(Observer)/发布-订阅(Publish–subscribe)模式

```js
function Observer () {
  this._subjects = [];
  this._eventCount = 0;
}

Observer.prototype.on = function (eventName, listener) {
  if (this._subjects.filter(e => e.listenerName === listener.name).length) {
    return;
  }

  this._subjects.push({
    name: eventName,
    listener,
    listenerName: listener.name || null,
    id: this._eventCount,
  });

  this._eventCount += 1;
};

Observer.prototype.removeListener = function (eventName, listener) {
  if (listener.name) {
    this._subjects = this._subjects.filter(e => e.listenerName !== listener.name);
  }
};

Observer.prototype.emmit = function (eventName, ...args) {
  const _subjects = this._subjects.filter(e => e.name === eventName);

  _subjects.forEach(subject => subject.listener(...args));
};
```
```js
const foo = new Emitter();

foo.on('message', (e) => {
  console.log(e);
});

foo.emit('message', 'a', 'b');
foo.emit('not a message', 'c');
```

```js
const Emitter = {
  on: function (eventName, listener) {
    if (!this.callStack) {
      Object.defineProperty(this, "callStack", {
        value: [],
        configurable: true,
        writable: true,
      });
    }

    this.callStack.push({
      name: eventName,
      listener,
    });
  },
  emit: function (eventName) {
    Array.prototype.slice.call(arguments, 1).forEach(arg => {
      this.callStack.find(e => e.name === eventName)
      && this.callStack.find(e => e.name === eventName).listener(arg);
    });
  },
};
```

```js
const personA = Object.assign({}, Emitter);
const personB = Object.assign({}, Emitter);

personA.on('messageA', (e) => {
  console.log(e);
});
personB.on('messageB', (e) => {
  console.log(e);
});

personA.emit('messageA', 'a1');
personA.emit('messageB', 'a2');
personB.emit('messageA', 'b1');
personB.emit('messageB', 'b2');
```

## 外观(Facade)模式
```js
function setStyles(elementids, styles) {
  elementids.forEach(id => {
    const element = document.getElementById(id);
    for (let property in styles) {
      if ({}.hasOwnProperty.call(styles, property)) {
        element.style[property] = styles[property];
      }
    }
  });
}
```
