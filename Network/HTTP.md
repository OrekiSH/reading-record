> The target of an HTTP request is called a "resource"

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

## HTTP/1.1

- 连接可复用
- 允许在第一个应答被完全发送之前发送第二个请求
- 分块传输
- 缓存控制机制
- 内容协商机制(Accept-)

## 3×× Redirection

```js
- 301 Moved Permanently // a user agent MAY change the request method from POST to GET for the subsequent request(cached)
- 302 Found // a user agent MAY change the request method from POST to GET for the subsequent request
- 304 Not Modified
- 307 Temporary Redirect
- 308 Permanent Redirect // cached

// window.location.href = '<url>';
// <meta http-equiv="refresh" content="<seconds>; url=<url>" />
// redirect 301 <old-file> <new-file>
```

---
### 缓存
当 web 缓存发现请求的资源已经被存储，它会拦截请求，返回该资源的拷贝，而不会去源服务器重新下载．缓存分为浏览器与代理缓存，网关缓存，CDN缓存，反向代理缓存和负载均衡器.

>*response_is_fresh = (freshness_lifetime > current_age)*

current_age大致等于当前本地时间-请求发送的本地时间
```
/** 
  age_value: Age header field
  
  now: the current value of the clock at the host performing the calculation
  
  request_time: The current value of the clock at the host at the time the request resulting in the stored response was made
*/
apparent_age = max(0, response_time - date_value);
response_delay = response_time - request_time;
corrected_age_value = age_value + response_delay;
corrected_initial_age = max(apparent_age, corrected_age_value);
resident_time = now - response_time;
current_age = corrected_initial_age + resident_time;
```

#### 规则

- **max-age**: 缓存机制中max-age的优先级是**最高**的, 因此响应中存在`max-age`时, freshness_liftime便等于`max-age`的值

- **Expires**: 若响应中不存在Control-control: max-age,则检查`Expires`的值, freshness_lifetime = expires_value - date_value, 若响应头中不存在Date,则date_value便为浏览器收到响应的本地(local)时间

- **Last-Modified**: 同理, freshness_lifetime = (date_value - last_modified_value) * 0.10

```
/* 不缓存 Not fresh */
Cache-Control: no-cache
Pragma: no-cache  /* chromium treat "Pragma: no-cache" as a synonym for "Cache-Control: no-cache" even though RFC 2616 does not specify it. */
Cache-Control: no-store
Vary: *
Cache-Control: must-revalidate
Expires: <date in the past>
```
```
# 隐式缓存
response_code_ == 300 
response_code_ == 301 (Moved Permanently)
response_code_ == 308 (Permanent Redirect)
response_code_ == 410
```
