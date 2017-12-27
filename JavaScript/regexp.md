## characters

- `\`
```js
const regex = /a*/;
const regex = /a\*/;
```
- `^`,`$`
- `*`: {0,}, `+`: {1,}, `?`: {0, 1}
- `.`: 匹配除换行符之外的任何单个字符
```js
/.n/.exec('nay, an apple is o\nn the tree');
```
- `x|y`: 匹配'x'或'y',若两者都匹配到取前者
- `(x)`: 匹配 'x' 并且记住匹配项(捕获括号, capturing parentheses)
- `(?:x)`: 匹配 'x' 但是不记住匹配项(非捕获括号)

- `x(?=y)`: 匹配'x'仅当'x'后面跟着'y'(lookahead)
```js
/Jack(?=Sprat)/.exec('Jack Sprat');
/Jack(?=Sprat|Frost)/.exec('JackFrost');
```
- `x(?!y)`: 匹配'x'仅当'x'后面不跟着'y'(negated lookahead)
```js
/\d+(?!\.)/.exec(3.141);
```
- `\w`: `[A-Za-z0-9_]`
- `\b`: 匹配一个词的边界

```js
/oo\b/.exec('moon');
```

```js
const ipRegExp = /^(?!0)(?!.*\.$)((1?\d?\d|25[0-5]|2[0-4]\d)(\.|$)){4}$/;
const commentRegExp = /\/\*[\s\S]*?\*\/|([^:"'=]|^)\/\/.*$/mg;
const cjsRequireRegExp = /[^.]\s*require\s*\(\s*["']([^'"\s]+)["']\s*\)/g;
```
