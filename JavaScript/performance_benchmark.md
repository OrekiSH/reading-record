## Date now
```js
// Firefox 55.0.2
cached performance object (1,921,024,377)
performance.now()         (1,840,944,932)
Date.now()                   (28,088,723)
new Date().getTime()          (6,061,578)
new Date().valueOf()          (5,964,633)
+new Date()                   (3,071,180)
```

```js
// chrome 59.0.3071.115
Date.now()                   (20,097,131)
new Date().getTime()         (10,206,330)
cached performance object    (10,007,552)
new Date().valueOf()          (9,844,267)
+new Date()                   (6,222,987)
performance.now()             (3,350,027)
```

## style vs class
```js
// chrome 59.0.3071.115
element.className                     (1,203,618)
element.setAttribute('class', '');    (1,003,588)
element.setAttribute('style', '');      (208,468)
element.style.cssText
element.style[property]                 (179,678)
```

## forEach
```js
// Firefox 55.0.2
for with caching            (935,101)
_.forEach(while)            (916,222)
for                         (880,727)
Array.forEach               (769,200)
for in
```
```js
// chrome 59.0.3071.115
for                         (830,140)
for with caching            (767,608)
_.forEach(while)            (690,650)
Array.forEach               (422,247)
for in
```

## Array.isArray vs Array.prototype.toString.call
```js
// Firefox 55.0.2
Array.isArray       (2,005,652,450)
instanceof Array    (1,911,794,158)
toString.call          (45,463,099)
```
```js
// chrome 59.0.3071.115
Array.isArray       (740,810,518)
instanceof Array    (172,834,324)
toString.call        (28,445,158)
```
