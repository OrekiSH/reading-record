## HTMLCanvasElement

- canvas元素内容部分包含的文本被称为后备内容(fallback content)
- 默认情况下，canvas元素背景色与其父元素背景色一致
- canvas元素的width和height在规范中被定义为非负整数, 虽然浏览器普遍允许使用px后缀的字符串
- canvas元素有两套尺寸: 元素本身大小和元素绘图表面(drawing surface)的大小。设置元素的width/height属性时，同时修改了两套尺寸的大小, 而通过样式设置只修改元素本身的大小, 浏览器会将绘图表面缩放到元素大小。
- HTMLCanvasElement: `getContext`, `toDataURL(type, quality)`, `toBlob(callback, type, args)`

```html
<canvas id="c1" width="600" height="300"></canvas>
<canvas id="c2" style="width: 600px; height: 300px;"></canvas>

<script>
  const c1 = document.getElementById('c1');
  const ctx1 = c1.getContext('2d');
  ctx1.textAlign = 'center';
  ctx1.fillText('hello, world', c1.width / 2, c1.height / 2);

  const c2 = document.getElementById('c2');
  const ctx2 = c2.getContext('2d');
  ctx2.textBaseline = 'middle';
  ctx2.fillText('hello, world', c2.width / 2, c2.height / 2);
  // ctx2.fillText('hello, world', c2.getBoundingClientRect().width / 2, c2.getBoundingClientRect().height / 2);
</script>
```

## CanvasRenderingContext2D
- 和Adobe Illustrator与Cocoa一样，canvas也是先创建不可见的路径，在调用stroke()来描绘边缘，或是调用fill()来对路径内部进行填充使得路径可见.
- `ctx.arc(x, y, radius, startAngle, endAngle [, anticlockwise])`
- `ctx.quadraticCurveTo(cpx, cpy, x, y)`
- `CanvasGradient ctx.createLinearGradient(x0, y0, x1, y1)`
- `ctx.bezierCurveTo(cp1x, cp1y, cp2x, cp2y, x, y)`
