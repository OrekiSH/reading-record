## 跨域资源共享(CORS)

浏览器被限制为只能发送简单的CORS请求
- `GET`
- `HEAD`
- `POST` && (`Content-Type: text/plain` || `Content-Type: multipart/form-data` || `Content-Type: application/x-www-form-urlencoded`) 

浏览器在发送一个非简单CORS请求(或是包含CORS请求禁止首部的请求)前必须发送一个预检(preflight)请求

```js
// OPTIONS request with `Access-Control-Request-Method: POST` header
request.open('POST', url);
request.setRequestHeader('Content-Type', 'application/xml');
```

CORS请求中安全的首部
- `Accept`, `Accept-Language`
- `Content-Language`, `Content-Type`

CORS请求中禁止的首部
- `Accept-Charset`,  `Accept-Encoding`, `Access-Control-*`
- `Host`, `Upgrade`, `Connection`, `Origin`, `Referer`
- `Cookie`, `Sec-*`, `Proxy-*`

CORS请求会忽略cookie和HTTP认证等用户凭证


请求发出后浏览器自动添加`Origin`首部, 远程服务器检查`Origin`首部后决定是否接受请求:接受就在响应中返回`Access-Control-*`首部, 反之则不在响应返回`Access-Control-*`首部(浏览器将自动作废之前发出的请求)

```js
const request = new XMLHttpRequest();
request.open('GET', 'https://third-party.com/not-allowed-access-resource.js');
request.onload = function () {};
request.send();
```

## 下载

- ArrayBuffer
- Blob
- Document
- JSON
- Text

```js
request.responseType = type;
```
