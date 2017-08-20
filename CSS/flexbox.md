## flex container
```css
.container {
  flex-direction: row | row-reverse | column | column-reverse;
  flex-wrap: nowrap | wrap | wrap-reverse;
  /* default is row nowrap */
  flex-flow: <flex-direction> <flex-wrap>;

  /* space-between: first(last) item is on the start(end) line */
  justify-content: flex-start | flex-end | center | space-between | space-around | (space-evenly);

  align-items: stretch | flex-start | flex-end | center | baseline;

  /* this property has no effect on a single-line flex container */
  align-content: stretch | flex-start | flex-end | center | space-between | space-around;
}
```

## flex items

float, clear and vertical-align have no effect on a flex item.

the margins of adjacent flex items do not collapse.

```css
.item {
  order: <integer>;
  flex-grow: <number>; /* default 0 */
  flex-shrink: <number> /* default 1 */
  flex-basis: <length> | auto; /* default auto */

  flex: none | <flex-grow> (<flex-shrink> <flex-basis>);
  align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
```
