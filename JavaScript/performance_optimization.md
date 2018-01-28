## 资源加载策略
- 离线包
- minify, uglify, concat
- 资源按需加载, 模块按需加载(code splitting的三种方式)
- 减少不必要的网络通信
- 图片资源加载: Base64, WebP, sharpP

## 缓存
- localStorage
- 静态资源缓存(cache-control: max-age=3600, expires: *)

## 网络通信
- DNS预获取: `<meta http-equiv="x-dns-prefetch-control" content="on">`, `<link rel="dns-prefetch" href="">`

## 页面直出
- Next.js
- Koa/Express

## 数据上报合并
- 采集数据->队列->压缩后生成URL上传

## 首屏/白屏时间
1.DOMContentLoaded
- 触发时间点: HTML文档被加载和解析完成后触发.
- 浏览器解析HTML文档的原理和产物
- JavaScript阻塞DOM生成: 在解析文档时遇到<script>标签将会停止文档的解析，转而去加载/执行脚本.
- 脚本在CSS被解析后(生成CSSOM)才能执行: JS可以获取样式
- defer: 文档解析完成后加载脚本
- saync: 脚本加载完成后继续解析文档
- $(document).ready / $(document).load

## 渲染
- 重排(layout/reflow): 计算各元素几何信息(大小及在页面中的位置)的过程.重排几乎总是作用到整个文档
- 避免强制同步布局/布局抖动: JavaScript->Style->Layout->Paint->Composite
