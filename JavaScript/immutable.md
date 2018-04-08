## immutable.js
```js
const data = {
  chart: {
    error: null,
    result: [{
      indicators: {
        quote: {
          close: [3123.966796875, 3115.5234375, 3103.2578125],
        },
      },
      timestamp: [1522287000, 1522287900, 1522288800],
    }, {
      indicators: {
        quote: {
          close: [3123.966796875, 3115.5234375, 3103.2578125],
        },
      },
      timestamp: [1522287000, 1522287900, 1522288800],
    }],
  },
};

function format(timestamp) {
  const handleDigits = n => {
    return (n < 10) ? `0${n}` : n;
  };

  const date = new Date(timestamp);
  const y = date.getFullYear();
  const M = date.getMonth() + 1;
  const d = date.getDate();
  const H = date.getHours();
  const m = handleDigits(date.getMinutes());
  const s = handleDigits(date.getSeconds());

  const time = `${H}:${m}:${s}`;
  const standDate = `${y}-${M}-${d}`;

  return {
    full: `${standDate} ${time}`,
    date: standDate,
    time,
  };
}

// bad enough
function changeToDateTime(json) {
  json.chart.result.forEach(e => {
    e.timestamp = e.timestamp.map(item => format(item * 1000).full);
  });

  return json;
}

// still bad
function changeToDateTime2(json) {
  let copy = { ...json };

  copy.chart.result.forEach(e => {
    e.timestamp = e.timestamp.map(item => format(item * 1000).full);
  });

  return copy;
}

// awesome
function changeToDateTime3(json) {
  // return Immutable.updateIn(json, ['chart', 'result', 0, 'timestamp', 0], val => format(val * 1000).full);

  return Immutable.updateIn(
    json,
    'chart.result'.split('.'),
    result => result.map(obj => {
      return Immutable.updateIn(obj, ['timestamp'], t => t.map(val => format(val * 1000).full));
    }),
  );
}
```

## eslint-plugin-immutable

- no-let
- no-this: use [recompose](https://github.com/acdlite/recompose) in lifecycle methods
- no-mutation: `const point = { x: 23, y: 44 }; point.x = 99; // This is legal`, use `Object.freeze()` to prevent mutations
