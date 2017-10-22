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
