
|no|description|
|--|-----------|
|118|[Pascal's Triangle](https://leetcode.com/problems/pascals-triangle/description/)|
|119|[Pascal's Triangle II](https://leetcode.com/problems/pascals-triangle-ii/description/)|
|121|[Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/description/)|
|16|[3Sum Closest](https://leetcode.com/problems/3sum-closest/description/)|

## easy

#### Pascal's Triangle
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
```

#### Pascal's Triangle II
```js
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

#### Best Time to Buy and Sell Stock
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

## medium

#### 3Sum Closest
```js
function threeSumClosest (nums, target) {
  nums = nums.sort((a, b) => a - b);
  let res = nums[0] + nums[1] + nums[2];

  for (let i = 0; i < nums.length - 2; i++) {
    let j = i + 1;
    let k = nums.length - 1;

    while (j < k) {
      let num = nums[i] + nums[j] + nums[k];

      if (Math.abs(num - target) < Math.abs(res - target)) res = num;

      if (num < target) j += 1;
      else k -= 1;
    }
  }

  return res;
};
```
