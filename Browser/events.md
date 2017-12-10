```html
<article>
  <section id="header">
    <ul id="nav">sign in</ul>
  </section>
  <section id="footer"></section>
</article>
```

## capture and bubble
```js
const nav = document.getElementById('nav');
const header = document.getElementById('header');
const footer = document.getElementById('footer');
const article = document.getElementsByTagName('article')[0];
const list = [nav, header, footer, article, document.body, document.documentElement, document, window];
list.forEach(el => {
  el.addEventListener('click', () => console.log(el));
});

list.forEach(el => {
  el.addEventListener('click', () => console.log(el), true);
});

list.forEach(el => {
  el.addEventListener('click', () => console.log(el), { once: true, passive: true });
});
```

## Event
```js
const cb = e => console.log(e.target, e.currentTarget, e.eventPhase);
const event = new Event('abort', { bubbles: true });

document.querySelector(':root').addEventListener('abort', cb);
document.getElementById('nav').dispatchEvent(event);
```
