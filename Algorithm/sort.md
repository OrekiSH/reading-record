```js
function swap (array, m, n) {
  const tmp = array[m];
  array[m] = array[n];
  array[n] = tmp;
}
```
## bubble

```
[2, 4, 1, 3, 5]
2 4 1 3 5
2 1 4 3 5
2 1 3 4 5
2 1 3 4 [5]
-----------
4 + 3 + 2 + 1
```

```js
function bubbleSort (array) {
  let stored = [...Object.create(array)];
  let count = array.length - 1;

  while (count) {
    for (let i = 0; i < count; i++) {
      if (stored[i] > stored[i + 1]) {
        swap(stored, i, i + 1);
      }
    }
    count -= 1;
  }

  return stored;
}
```

## selection

```
[2, 4, 1, 3, 5]
2 4 1 3 5
2 4 1 3 5
2 4 1 3 5
[1] 4 2 3 5
-------------
4 + 3 + 2 + 1
```

```js
function selectionSort (array) {
  let stored = [...Object.create(array)];
  const len = array.length;
  let minVal = 0;
  let minIndex = 0;

  for (let i = 0; i < len - 1; i++) {
    minIndex = i;
    minVal = stored[i];

    for (let j = i + 1; j < len; j++) {
      if (stored[j] < minVal) {
        minIndex = j;
        minVal = stored[j];
      }
    }

    swap(stored, minIndex, i);
  }

  return stored;
}
```

## insertion

```
[2, 4, 1, 3, 5]
[2 4] 1 3 5
-----------
2 1 4 3 5
[1 2 4] 3 5
-----------
1 2 3 4 5
[1 2 3 4] 5
------------
1 + 2 + 2 + 1
```

```js
function insertionSort (array) {
  let stored = [...Object.create(array)];

  for (let i = 1; i < stored.length; i++) {
    for (let j = i; j > 0; j--) {
      if (stored[j] < stored[j - 1]) {
        swap(stored, j, j - 1);
      } else {
        break;
      }
    }
  }

  return stored;
}
```

## merge

```js
function mergeSort (array) {
  let stored = [...Object.create(array)];

  function sort (array, start, end) {
    start = (start === undefined) ? 0 : start;
    end = (end === undefined) ? array.length - 1 : end;
    if (end - start < 1) {
      return;
    }
    let mid = (start + end) >> 1;
    let tmp = 0;

    sort(array, start, mid);
    sort(array, mid + 1, end);

    while (start <= mid && end >= mid + 1) {
      if (array[start] >= array[mid + 1]) {
        tmp = array[mid + 1];
        for (let i = mid; i >= start; i--) {
          array[i + 1] = array[i];
        }
        array[start] = tmp;
        mid += 1;
      } else {
        start += 1;
      }
    }

    return array;
  }

  return sort(stored);
}
```
