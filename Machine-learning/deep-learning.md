## Gradient Descent

```js
// y = wx^2 + bx
const target = x => 3 * Math.pow(x, 2) + 2 * x;

const input = [...Array(100).keys()].map(e => e - 50);
const output = [...Array(100).keys()].map(e => (target(e - 50) + Math.random() * 3));

// [w, b]
const solution = [0, 0];
const hypothesis = (x, params) => params[0] * Math.pow(x, 2) + params[1] * x;

const errorOfHypothesis = params => {
  let SST = 0;
  input.forEach((e, index) => {
    SST += Math.pow((hypothesis(e, params) - output[index]), 2)
  });

  return Math.sqrt(SST / input.length);
};

const calculateGradientDescent = stepSize => {
  const error = errorOfHypothesis(solution);
  const delta = 1e-8;

  let gradient = [];
  for (let i in solution) {
    let deltaSolution = [];
    deltaSolution[0] = solution[0];
    deltaSolution[1] = solution[1];

    deltaSolution[i] += delta;
    gradient[i] = (errorOfHypothesis(deltaSolution) - error) / delta;
  }

  for (let i in solution) {
    solution[i] -= gradient[i] * stepSize;
  }

  return error;
}

function run (iterations, stepSize) {
  const result = [...new Array(iterations).keys()].map(e => {
    console.log(solution);
    
    return calculateGradientDescent(stepSize);
  });

  return Math.min(...result);
}
```
