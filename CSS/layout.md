## 三栏布局

### grid
```css
.container {
  display: grid;
  grid-template-columns: 300px auto 300px;
}
```

### flexbox
```css
.container {
  display: flex;
}
.container .left, .right {
  flex: 300px 0 0;
}
.container .right {
  order: 1;
}
.container .content {
  flex: 1;
}
```

### table
```css
.container {
  width: 100%;
  display: table;
}
.container .left, .content, .right {
  display: table-cell;
  height: 100%;
}
.container .left, .right {
  width: 300px;
}
```

### absolute positioning
```css
html, body {
  margin: 0;
}
.container {
  position: relative;
  height: 200px;
}
.container .left, .right {
  position: absolute;
  top: 0;
  width: 300px;
  height: 100%;
}
.container .left {
  left: 0;
}
.container .right {
  right: 0;
}
.container .content {
  margin: 0 300px;
  height: 100%;
}
```

### float
```html
<article class="container">
  <section class="left">left</section>
  <section class="right">right</section>
  <section class="content">content</section>
</article>
```
```css
.container {
  height: 200px;
}
.container .left, .right {
  width: 300px;
  height: 100%;
}
.container .left {
  float: left;
}
.container .right {
  float: right;
}
.container .content {
  margin: 0 300px;
  height: 100%;
  /*clear: both;*/
}
```

### 双飞翼
```html
<article class="container">
  <section class="content">content</section>
</article>
<section class="left">left</section>
<section class="right">right</section>
```
```css
html, body {
  margin: 0;
  height: 200px;
}
.container {
  float: left;
  width: 100%;
  height: 100%;
}
.container .content {
  margin: 0 300px;
  height: 100%;
}
.left, .right {
  float: left;
  width: 300px;
  height: 100%;
}
.left {
  margin-left: -100%;
}
.right {
  margin-left: -300px;
}
```

## 垂直居中

```css
.container {
  width: 500px;
  text-align: center;
}
```
```html
<main class="container">
  <h1>Am I centered yet?</h1>
  <p>Center me, please!</p>
</main>
```

### absolute positioning
```css
.container {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  /*transform-style: preserve-3d;*/
}
```
> 在某些浏览器中,这个方法可能会导致元素的显示有一些模糊,因为元素可能被放置在半个像素上

### 50% viewport margin
```css
.container {
  margin: 50vh auto 0;
  transform: translateY(-50%);
}
```

### flexbox
```html
<section class="main">
  <main class="container">
    <h1>Am I centered yet?</h1>
    <p>Center me, please!</p>
  </main>
</section>
```
```css
/*plan A*/
.main {
  display: flex;
  min-height: 100vh;
}
.container {
  margin: auto;
}
```
```css
/*plan B*/
.main {
  display: flex;
  min-height: 100vh;
  justify-content: center;
  align-items: center;
}
```
