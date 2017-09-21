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

while (new Date() - start < 1000) {}
```

## microtask queue
