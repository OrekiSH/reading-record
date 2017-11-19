## promise

```js
function getJSON (url) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open("GET", url);
    xhr.onload = () => resolve(xhr.responseText);
    xhr.onerror = () => reject(xhr.statusText);
    xhr.send();
  });
}
```
```js
const p1 = Promise.all([0, Promise.resolve(1)]);

const p2 = new Promise((resolve, reject) => {
  resolve(2);
});

const p3 = 3;

const p4 = new Promise((resolve, reject) => {
  setTimeout(resolve, 100, 4);
}); 
Promise.all([p1, p2, p3]).then(vals => console.log(vals));
```

```js
function P (executor) {
  var promise = this;
  promise.status = 'pending';
  promise.data = void 0;
  promise.onResolveddCallback = [];
  promise.onRejectededCallback = [];

  function resolve (value) {
    if (promise.status === 'pending') {
      promise.status = 'resolved';
      promise.data = value;
      var callbacks = promise.onResolveddCallback;

      for (var i = 0; i < callbacks.length; i++) {
        callbacks[i](value);
      }
    }
  }

  function reject (reason) {
    if (promise.status === 'pending') {
      promise.status = 'rejected';
      promise.data = reason;
      var callbacks = promise.onRejectededCallback;

      for (var i = 0; i < callbacks.length; i++) {
        callbacks[i](reason);
      }
    }
  }

  try {
    executor(resolve, reject);
  } catch (e) {
    reject(e);
  }
}
```

```js
P.prototype.then = function (onResolved, onRejected) {
  var promise = this;
  onResolved = typeof onResolved === 'function' ? onResolved : function (v) {};
  onRejected = typeof onRejected === 'function' ? onRejected : function (r) {};

  if (promise.status === 'pending') {
    return new P (function (resolve, reject) {
      promise.onResolveddCallback.push(function (value) {
        try {
          var x = onResolved(promise.data);
          if (x instanceof P) x.then(resolve, reject);
        } catch (e) {
          reject(e);
        }
      });

      promise.onResolveddCallback.push(function (reason) {
        try {
          var x = onRejected(promise.data);
          if (x instanceof P) x.then(resolve, reject);
        } catch (e) {
          reject(e);
        }
      });
    });
  }

  if (promise.status === 'resolve') {
    return new P (function (resolve, reject) {
      try {
        var x = onResolved(promise.data);
        if (x instanceof P) x.then(resolve, reject);
        resolve(x);
      } catch (e) {
        reject(e);
      }
    });
  }

  if (promise.status === 'reject') {
    return new P (function (resolve, reject) {
      try {
        var x = onRejected(promise.data);
        if (x instanceof P) x.then(resolve, reject);
      } catch (e) {
        reject(e);
      }
    });
  }
};

P.prototype.catch = function (onRejected) {
  return this.then(null, onRejected);
};
```
