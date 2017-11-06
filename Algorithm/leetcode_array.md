## easy

```js
function pascalTriangle (n) {
  const result = [[], [[1]]];
  if (n < 2) {
    return result[n];
  }

  const tri = pascalTriangle(n - 1);
  const last = tri[tri.length - 1];
  const left = [0].concat(last);
  const right = last.concat([0]);

  return tri.concat([
    right.map((e, i) => left[i] + e),
  ]);
}

function pascalTriangleII (n) {
  if (n < 1) return [[1]][n];

  let last = [1];
  let tri = [last];
  for (let i = 1; i <= n; i++) {
    const left = [0].concat(last);
    const right = last.concat([0]);

    last = right.map((e, i) => left[i] + e);
    tri = tri.concat([last]);
  }

  return tri[n];
}
```

```js
// Best Time to Buy and Sell Stock
function maxProfit (prices) {
  if (!Array.isArray(prices) || prices.length === 0) return 0;

  let max = 0;
  let min = prices[0];
  prices.forEach(e => {
    min = Math.min(min, e);
    max = Math.max(max, e - min);
  });

  return max;
}
```
