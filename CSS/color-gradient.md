## 线性渐变(linear gradient/axial gradient)

- 线性插值(linear interpolation): y = y0 + (x - x0) * (y1 - y0) / (x1 - x0)
- `linear-gradient([<angle>|to <side-or-corner>,]?<color-stop> [,<color-stop>]+)`
- 条纹: 如果多个色标具有相同的位置，它们会产生一个无限小的过渡区域，过渡的起止色分别是第一个和最后一个指定值。如果某个色标的位置比整个列表中在它之前的色标的位置值都要小，则该色标的位置会被设为它前面所有色标位置的最大值。
- 网格: 使用长度而不是百分比作为色标

```scss
/* 等宽水平条纹 */
background: linear-gradient(#fb3 50%, #58a 50%);
/* 不等宽水平条纹 */
background: linear-gradient(#fb3 30%, #58a 0);
/* 三色水平条纹 */
background: linear-gradient(#fb3 33.3%, #58a 0, #58a 66.6%, yellowgreen 0);
/* 三色垂直条纹 */
background: linear-gradient(90deg/* to right */, #fb3 33.3%, #58a 0, #58a 66.6%, yellowgreen 0);
/* 斜向条纹 */
background: repeating-linear-gradient(75deg, #fb3, #fb3 15px, #58a 0, #58a 30px);
/* 随机条纹 */
background: hsl(20, 40%, 90%);
background-image:
  linear-gradient(90deg, #fb3 11px, transparent 0),
  linear-gradient(90deg, #ab4 23px, transparent 0),
  linear-gradient(90deg, #655 41px, transparent 0);
background-size: 41px 100%, 61px 100%, 83px 100%;

/* 网格 */
background: #58a;
background-image: linear-gradient(white 1px, transparent 0),
  linear-gradient(90deg, white 1px, transparent 0);

/* 斜面切角 */
background: #58a;
background: linear-gradient(-45deg, transparent 15px, #58a 0);
```
## 径向渐变(radial gradient)

- 近似通过球体来自点源的光的漫反射
