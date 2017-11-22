## Representational State Transfer

- Runtime Abstraction
- ELements
- Components
- Connectors

## HTTP Verbs

- `HEAD`: Can be issued against any resource to get just the HTTP header info.
- `PUT`: Used for replacing resources or collections. For PUT requests with no body attribute, be sure to set the `Content-Length` eader to zero.
- `PATCH`: Used for updating resources with partial JSON data. For instance, an Issue resource has `title` and `body` attributes. A PATCH request may accept one or more of the attributes to update the resource. PATCH is a relatively new and uncommon HTTP verb, so resource endpoints also accept POST requests.

## Rate Limiting

- `X-RateLimit-Limit`: The maximum number of requests you're permitted to make per `hour`.
- `X-RateLimit-Remaining`: The number of requests remaining in the current rate limit window.
- `X-RateLimit-Reset`: The time at which the current rate limit window resets in UTC epoch seconds.

## User Agent Required

> All API requests MUST include a valid User-Agent header. Requests with no User-Agent header will be rejected(403). 

## JSON-P Callbacks

```js
curl https://api.github.com?callback=foo
function foo(response) {
  var meta = response.meta;
  var data = response.data;
  console.log(meta);
  console.log(data);
}

var script = document.createElement('script');
script.src = 'https://api.github.com?callback=foo';

document.getElementsByTagName('head')[0].appendChild(script);
```
