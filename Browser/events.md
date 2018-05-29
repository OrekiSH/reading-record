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

## MouseEvent
> mousedown -> mouseenter/mouseover -> mousemove -> mouseleave/mouseout -> mouseup

- target
- currentTarget
- relatedTarget: 例如`mouseover`和`mouseenter`为互补事件(complementary event), `mouseover`会冒泡, `mouseenter`不会冒泡
- clientX / clientY (alias `x` / `y`)
- offsetX / offsetY: 事件对象与目标节点padding edge的偏移量(即事件作用于border上时为负值)
- screenX / screenY: 包含浏览器的标签页,地址栏等，相对于屏幕的顶点
- pageX / pageY[Chrome45, IE9]: 包含滚动条滚动的距离
- MouseEvent不会作用于margin(`:hover`等伪类选择器也是一样)
- 用MouseEvent实现Drag时需要注意的是`mousemove`与`mouseup`事件监听的元素应该为document
