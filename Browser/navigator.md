## serviceWorker

```js
const script = document.createElement('script');
script.src = '/js/script.js';
document.body.appendChild(script);

if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('/service-worker.js')
    .then(registration => {
      console.log(registration);
    })
    .catch(error => {
      console.log(error);
    });
}
```

```js
// service-worker.js
const cacheName = 'helloWorld';
self.addEventListener('install', event => {
  event.waitUntil(
    caches.open(cacheName)
      .then(cache => cache.addAll([
        'js/script.js',
      ])),
  );
});

self.addEventListener('fetch', event => {
  event.respondWith(
    caches.match(event.request)
      .then(res => {
        if (res) {
          return res;
        }

        return fetch(event.request);
      }),
  );
});
```
