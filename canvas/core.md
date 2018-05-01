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
- `ctx.arc(x, y, radius, startAngle, endAngle [, anticlockwise])`, anticlockwise默认值为false，即圆弧沿顺时针方向绘制
- `ctx.quadraticCurveTo(cpx, cpy, x, y)`
- `CanvasGradient ctx.createLinearGradient(x0, y0, x1, y1)`
- `ctx.bezierCurveTo(cp1x, cp1y, cp2x, cp2y, x, y)`
- canvas元素采用立即模式(immediate-mode)来绘制图形，绘图系统不会包含将要绘制的图形对象列表
- 动画帧每秒移动的像素数: 物体每秒移动像素数和当前动画的帧数率(frame rate, 即当前动画每帧持续的毫秒数), (毫秒 / 帧) * (像素 / 秒)

## canvas的绘制模型
- 将图形或图像绘制到一个无限大的透明位图中，在绘制时遵循当前填充模式，描边模式及线条样式
- 将图形或图像的阴影以当前绘图环境的阴影设定绘制到另一幅位图中
- 将阴影中每个像素的alpha分量乘以绘图环境对象的globalAlpha属性值
- 将绘有阴影的位图和经过剪辑区域剪切的canvas进行图像合成
- 将图形或图像每个像素的颜色分量乘以绘图环境对象的globalAlpha属性值
- 将绘有图形或图像的位图合成到经过剪辑区域剪切的canvas位图上

## 绘制
- 剪辑区域(clipping region): 在canvas中由路径定义的一块区域，浏览器会将所有绘图操作限制在该区域内进行，剪辑区域默认为整个canvas元素的大小　
- 清除像素: 将颜色设置为全透明的黑色
- 对一幅图像进行多次连续高斯模糊的效果与一次更大的高斯模糊可以产生同样的效果，大的高斯模糊的半径是所用多个高斯模糊半径平方和的平方根。例如，使用半径分别为6和8的两次高斯模糊变换得到的效果等同于一次半径为10的高斯模糊效果
- 在某一时刻，canvas中只能有一条路径存在，被称为当前路径(current path), 当前路径可以包含许多子路径, 利用`beginPath`会清除当前路径的所有子路径
- 非零环绕(nonzero winding)原则: 从路径中任意给定区域画一条足够长的线段，使线段终点位于区域外部。初始化计数器为0, 与路径的顺时针部分相交加1，顺时针部分相交减1。若计数器最终值不为0,则该区域在路径内部,填充该区域。
- 根据Canvas规范，当使用`arc()`方法在当前路径增加子路径时，必须将上条子路径的终点和圆弧的起点相连。
- 如果在两个像素的边界绘制1像素宽的线段，线段将占据两个像素的区域; 如果在像素边界绘制1像素宽的线段，线段将会有0.5个像素在边界中线的左边，另0.5个像素在边界中线的右边.
