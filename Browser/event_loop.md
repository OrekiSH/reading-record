## rending(layout) engine

- Chrome: Blink
- Firefox: Gecko
- Edge: EdgeHTML
- IE: Trident
- Safari: Webkit
- Opera: Presto -> Blink

## JavaScript Engine

- Chrome: v8
- Firefox: SpiderMonkey
- Edge: Chakra
- IE: Chakra
- Safari: JavaScriptCore
- Opera: Futhark -> Carakan -> v8

Memory Heap(where the memory allocation happens)
Call Stack(where your stack frames are as your code executes)

v8的主线程用于获取(fetch), 编译, 执行代码, 当主线程正在编译的时候会用另一个线程优化代码, 其他线程用来GC
Full-codegen编译器将JS代码编译成机器码(machine code), CrankShaft编译器用来优化JS代码

## 消息通信机制

- 同步与异步
- 阻塞与非阻塞

## task queue(macrotasks queue)

> A browsing context is an environment in which Document objects are presented to the user.

```js
const start = Date.now();
console.log(start);

setTimeout(() => {
  const end = Date.now();

  console.log(end);
  console.log(end - start);
}, 800);

setTimeout(function () {
  const end = Date.now();

  console.log(end);
  console.log(end - start);
}, 800);

while (Date.now() - start < 1000) {}
```
```js
// when call stack is empty, the main thread go to read the task queue
const start = Date.now();

setTimeout(() => {
  const end = Date.now();
  console.log(`900ms: ${end - start}`);

  while (Date.now() - end < 2000) {}
}, 900);

setTimeout(function () {
  const end = Date.now();
  console.log(`800ms: ${end - start}`);
}, 800);

while (Date.now() - start < 1000) {}
```
```js
const start = Date.now();

setImmediate(() => {
  console.log(Date.now() - start);
});

setImmediate(function () {
  console.log(Date.now() - start);
});

while (Date.now() - start < 1000) {}
```

```js
setTimeout(() => {
  console.log(`1:${Date.now() - start}`);
  setTimeout(() => {
    console.log(`4:${Date.now() - start}`);
  });
  setImmediate(() => {
    console.log(`5:${Date.now() - start}`);
  });
});

setImmediate(() => {
  console.log(`2:${Date.now() - start}`);
  setImmediate(() => {
    console.log(`6:${Date.now() - start}`);
  });
  setTimeout(() => {
    console.log(`7:${Date.now() - start}`);
  });
});
```

## microtask queue

```js
const start = Date.now();

process.nextTick(() => {
  const end = Date.now();
  console.log(end - start);
});

process.nextTick(function () {
  const end = Date.now();
  console.log(end - start); // 1000
});

process.nextTick(function () {
  const end = Date.now();
  console.log(end - start);
});

while (Date.now() - start < 1000) {}
```

```js
const start = Date.now();

setTimeout(() => {
  console.log(`1:${Date.now() - start}`);
}, 500);

setImmediate(() => {
  console.log(`2:${Date.now() - start}`);
});

process.nextTick(() => {
  console.log(`3:${Date.now() - start}`);
});

while (Date.now() - start < 1000) {}
```
