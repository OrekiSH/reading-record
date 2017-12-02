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
