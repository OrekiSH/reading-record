> The target of an HTTP request is called a "resource"

## HTTP/2

- 二进制分帧
- 多路复用(Multiplexing)
- 首部压缩
- Server Push

```
 _______________
|     应用层     |
|__|二进制分帧|__|
|_____会话层_____|
|_____传输层_____|
|_____网络层_____|
|     物理层     |
|_______________|  
```

## HTTP/1.1

- 持久连接,连接可复用
- Pipelining,允许在第一个应答被完全发送之前发送第二个请求
- 分块传输
- 缓存控制机制
- 内容协商机制(Accept-)

```js
Connection: keep-Alive // for HTTP/1.0
Keep-Alive: timeout=5, max=100
```

## 内容协商机制(mechanisms for content negotiation)

服务端驱动型(主动)内容协商: 客户端设置特定的 HTTP 首部

- `Accept`
- `Accept-Charset`(IE8不再发送该首部)
- `Accept-Encoding`
- `Accept-Language`
- [`User-Agent`]

用户代理驱动型(响应式)协商: 服务端返回状态码300或406, 脚本重定向

## [range request](https://tools.ietf.org/html/rfc7233)

- 请求成功，返回 `206 Partial Content`
- 请求的范围越界, 返回 `416 Requested Range Not Satisfiable`
- 不支持范围请求, 返回 `200 OK`

```js
Accept-Ranges: bytes/none
```

## HSTS (HTTP Strict Transport Security)
- 当使用301、302跳转时，只要修改location指令，网站就会被劫持
- 采用HSTS协议的网站将保证浏览器始终连接到该网站的HTTPS加密版本，不需要用户手动在URL地址栏中输入加密地址
- HSTS的作用还包括阻止基于SSL Strip的中间人攻击，万一证书有错误，则显示错误，用户不能回避警告
- `307 Internal Redirect`
- `Strict-Transport-Security: max-age=<expire-time>; includeSubDomains`
- `Strict-Transport-Security: max-age=<expire-time>; preload`


## OSI七层
- 应用层: DNS, HTTP
- 表示层
- 会话层
- 传输层: TCP, UDP
- 网络层: IP
- 数据链路层
- 物理层

## 重定向

```js
- 301 Moved Permanently // a user agent MAY change the request method from POST to GET for the subsequent request
- 302 Found // a user agent MAY change the request method from POST to GET for the subsequent request
- 304 Not Modified
- 307 Temporary Redirect
- 308 Permanent Redirect

// window.location.href = '<url>';
// <meta http-equiv="refresh" content="<seconds>; url=<url>" />
// redirect 301 <old-file> <new-file>
```
## 缓存

缓存分为浏览器与代理缓存，网关缓存，CDN缓存，反向代理缓存和负载均衡器.

> response_is_fresh = freshness_lifetime > current_age

- Cache-Control: max-age
- Expires: 若响应中不存在Control-control: max-age,则检查`Expires`的值, freshness_lifetime = expires_value - date_value, 若响应头中不存在Date,则date_value便为浏览器收到响应的本地(local)时间
- Last-Modified: freshness_lifetime = (date_value - last_modified_value) * 0.10

```js
// 强缓存(200 from cache),F5不检查强缓存
Cache-Control: public, max-age=86400
Expires: <GMT>
// 协商缓存(304)
// first request server return Last-Modified Header, then browser send If-Modified-Since Header, server return 304 without Last-Modified Header
Last-Modified: <GMT>
If-Modified-Since: <GMT>
// server return 304 with ETag Header
ETag: <string>
If-None-Match: <string>
```

```js
// 隐式缓存
response_code_ == 300
response_code_ == 301
response_code_ == 308
response_code_ == 410

// 不缓存
Cache-Control: no-cache
// chromium treat "Pragma: no-cache" as a synonym for "Cache-Control: no-cache" even though RFC 2616 does not specify it
Pragma: no-cache
Cache-Control: no-store
Vary: *
Cache-Control: must-revalidate
Expires: <date in the past>
```

```js
apparent_age = max(0, response_time - date_value);
response_delay = response_time - request_time;
corrected_age_value = age_value + response_delay;
corrected_initial_age = max(apparent_age, corrected_age_value);
resident_time = now - response_time;
current_age = corrected_initial_age + resident_time;
```

## Error

```js
400 Bad Request
401 Unauthorized
403 Forbidden
405 Method Not Allowed
500 Internal Server Error
503 Service Unavailable
```

## Uniform Resource Identifier
```js
protocol://host[:port][/path][?query][#fragment]

// data URIs
data:[<mediatype>][;base64],<data>
```

## URL规范化

- 删除Fragment
- 删除默认端口
- 删除空查询串('?'和'&')
- 删除默认文件名('.index.html')
- 删除多余的'www'
- 末尾加'/'
