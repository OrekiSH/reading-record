> The target of an HTTP request is called a "resource"

## HTTP/1.1

- 连接可复用
- 允许在第一个应答被完全发送之前发送第二个请求
- 分块传输
- 缓存控制机制
- 内容协商机制(Accept-)

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
