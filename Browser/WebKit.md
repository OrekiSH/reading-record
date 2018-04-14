## 浏览器与浏览器内核
- 
- 浏览器一战: 1998年IE取代Netscape Navigator成为主流浏览器
- 浏览器二战: 2003年后IE市场份额逐步受到其他浏览器蚕食
- 浏览器内核的作用: 将HTML/CSS/JavaScript文本及其相应资源文件转化为图像的模块
- 渲染引擎的组成内容: HTML解析器, CSS解析器, JavaScript引擎, 布局与绘图模块
- WebKit的历史: 起源于1998年KDE的KHTML与KJS，2002年发布JavaScriptCore,次年发布WebCore,2005年开源WebKit。2008年Google以WebCore和v8创建了Chromium, 2013年创建了Blink。
- Blink和WebKit的区别
- DOMContentLoaded和load事件的区别: DOM创建完成和资源加载完成
- timeline event: 加载(loading)事件, 脚本(scripting)事件, 渲染(rendering)事件, 绘制(painting)事件

## Chromium
- Chromium的多进程模型: Browser进程, Renderer进程, GPU进程
- Chromium的多线程模型: Browser进程的主线程为UI线程, 渲染线程, IO线程
- 渲染过程管线化: 渲染的不同阶段在不同的线程执行

## 资源加载
- 从资源池中查找资源的关键字: URL
- JS阻塞主线程, 如何加载HTML中后面使用的资源: WebKit会再开一个线程遍历资源
- 资源的生命周期: 资源加载后被放入资源池, 页面刷新后若资源仍在资源池内，则发送一个HTTP请求给服务器，说明资源在本地的一些信息, 若服务器判断信息未修改，则返回状态码304。
- If-Modified-Since: 给定的时间早于当前时间则更新资源, 否则响应体不返回内容(304)，只能在GET和HEAD中使用
- If-None-Match: 同If-Modified-Since，不过优先级更高
- LRU算法
- Chromium的多进程资源加载: Renderer进程的资源获取通过进程间通信将任务交给Browser进程, 使得资源在不同网页间共享变得容易。但同时Browser进程需要处理大量的请求，因此需要一个调度器根据URLRequest的标记和优先级来调度URLRequest对象。

## 网络栈
- Chromium网络栈的调用过程: URLRequest -> URLRequetJob -> HttpNetworkTransaction -> HttpSession -> HttpStream -> StreamSocket
- URLRequetJob对象被创建后,首先从Cookie管理器中获取与该URL相关联的信息
- 会话型Cookie和持续型Cookie: Session Cookie保存在内存中，在浏览器退出时清除; Persistent Cookie在有效期一直保留Cookie的内容
- DNS预读取和TCP预连接: DNS查询的评价时间是60-120ms, TCP三次握手也是几十毫秒左右.如何实现DNS预读取?
- HTTP管线化的优势和缺陷: HTTP Pipelining将多个HTTP请求填充在一个TCP数据包内, HOL bolcking问题
