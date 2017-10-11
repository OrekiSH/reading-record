## data URI scheme
```js
data:[<mediatype>][;base64],<data>
```
default mediatype: `text/plain;charset=US-ASCII`

```js
// DOMStrings are 16-bit-encoded strings
window.btoa(decodedData);
window.btoa(encodeURIComponent('你好').replace(/%([0-9A-F]{2})/g));
window.atob(encodedData);
```
```html
<!--if `file.js` is not in there,browser will start download and name it as `file.js`-->
<a href="file.js" download="not-a-file.js">file.js</a>

<!--name it as `not-a-file.js`-->
<a href="data:,Hello%2C%20World!" download="not-a-file.js">file.js</a>
```

## Blob(Android4.4+[-webkit])

[list of MIME types](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types)
```js
(function () {
  var link = document.createElement('a');
  var obj = new Blob(['some\n', 'text'], {
    type: 'application/msword',
  });
  link.href = URL.createObjectURL(obj);
  link.download = 'named.docx';
  link.click();
})();
```

## FileReader(IE10+ & Need polyfill)

```js
var obj = new Blob(['some\n', 'text'], {
  type: 'application/msword',
});
var reader = new FileReader();
reader.onloadend = function (e) {
  console.log(e.target.result);
};
reader.readAsText(obj);
```
