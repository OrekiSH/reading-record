注: 本文基于RFC2616和chromium61.0.3158.2
#### 大纲
- 消息 messages
- 连接管理 connection management
- 首部 headers
- 请求方法 request methods
- 响应状态码 response status codes
- 访问控制 access control
- 认证 authentication
- 缓存 caching
- cookies
- 重定向 redirects

---
#### 概述

> The target of an HTTP request is called a "resource"

**统一资源标识符的语法**

- 协议 protocol
- 主机 domain name
- 端口 port
- 路径 path to the file
- 查询 parameters
- 片段 anchor

**data URIs的语法**
```
data:[<mediatype>][;base64],<data>
```

**MIME类型**

- MIME类型是一种通知客户端其接收文件的多样性的机制,对大小写不敏感，但是传统写法都是小写.分为Discrete和Multipart类型
- 音频或视频文件只有正确设置了MIME类型才能被 `<video> `或`<audio> `识别和播放

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