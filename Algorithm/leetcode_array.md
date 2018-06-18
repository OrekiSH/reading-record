
|no|description|
|--|-----------|
|118|[Pascal's Triangle](https://leetcode.com/problems/pascals-triangle/description/)|
|119|[Pascal's Triangle II](https://leetcode.com/problems/pascals-triangle-ii/description/)|
|121|[Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/description/)|
|16|[3Sum Closest](https://leetcode.com/problems/3sum-closest/description/)|
|120|[Triangle](https://leetcode.com/problems/triangle/description/)|

## easy

#### Pascal's Triangle
```js
function pascalTriangle(n) {
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
function pascalTriangleII(n) {
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
function maxProfit(prices) {
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
function threeSumClosest(nums, target) {
  const n = nums.length;
  if (n < 3) return 0;

  const closest = nums[0] + nums[1] + nums[2];
  nums.sort((a, b) => a - b);

  for (let first = 0; first < n - 2; first++) {
    let second = first + 1;
    let third = n - 1;

    while (second < third) {
      const num = nums[first] + nums[second] + nums[third];

      if (num === target) return num;

      if (Math.abs(num - target) < Math.abs(closest - target)) closest = num;

      if (num < target) second += 1;
      else third -= 1;
    }
  }

  return closest;
};
```

#### Triangle
```js
function minimumTotal(triangle) {
  const n = triangle.length;
  let minlen = triangle[n - 1];

  for(let layer = n - 2; layer >= 0; layer--) {
    for(let i = 0; i <= layer; i++) {
      minlen[i] = Math.min(minlen[i], minlen[i + 1]) + triangle[layer][i];
    }
  }

  return minlen[0];
}
```
