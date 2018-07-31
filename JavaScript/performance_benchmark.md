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

## Double exclamation mark vs Boolean
```js
// Firefox 58.0.2
!!          (1,972,676,698)
Boolean     (1,898,041,676)
// Chrome 56.0.2924
!!            (989,776,011)
Boolean        (31,909,878)
// Chrome 64.0.3282
!!            (807,679,102)
Boolean       (776,490,904)
```

## Number vs. mul vs. parseInt vs. parseFloat
```js
// Firefox 58.0.2
parseInt     (45,218,564)
parseFloat   (34,378,613)
Number       (24,604,260)
*            (16,819,952)
// Chrome 64.0.3282
Number       (725,194,761)
*            (701,529,870)
parseFloat    (19,011,356)
parseInt      (15,761,237)
```

## String to Number
```js
// Chrome 68.0.3440.75
'' + num        (594,509,015)
String(num)     (110,114,193)
num.toString()   (55,379,822)
new String(num)  (18,274,764)
// Firefox 61.0.1
'' + num        (640,832,397)
new String(num)  (60,335,509)
String(num)      (36,538,554)
num.toString()   (35,419,064)
```

## preallocating Array
```js
// Chrome 68.0.3440.75
[];length = 10000;  (46,511)
new Array(10000);   (45,409)
[];                 (16,429)
// Firefox 61.0.1
new Array(10000);   (21,404)
[];length = 10000;  (18,554)
[];                 (12,671)


// Chrome 68.0.3440.75
new Array(100);     (4,187,720)
[];                 (1,548,981)
// Firefox 61.0.1
new Array(100);     (3,198,882)
[];                 (1,706,934)
```

## indexOf vs TripleEqual
```js
// Chrome 58.0.3029
Searching an array with indexOf               (114,763,824)
Searching array for a value with a for loop    (84,495,478)

// Chrome 68.0.3440.75
Searching array for a value with a for loop    (81,001,186)
Searching an array with indexOf                (58,772,812)

// Firefox 55.0
Searching array for a value with a for loop   (107,444,623)
Searching an array with indexOf                (62,336,421)

// Firefox 61.0.1
Searching an array with indexOf                (72,531,837)
Searching array for a value with a for loop    (71,648,889)

// IE 11
Searching array for a value with a for loop    (48,557,015)
Searching an array with indexOf                 (5,823,535)
```
