## flex container

```css
.container {
  /* when you set the direction to a reversed row or column, start and end are also reversed */
  flex-direction: row | row-reverse | column | column-reverse;
  
  justify-content: flex-start | flex-end | center | space-between | space-around | (space-evenly);
  
  align-items: stretch | flex-start | flex-end | center | baseline;
  
  flex-wrap: nowrap | wrap | wrap-reverse;
  
  flex-flow: <flex-direction> <flex-wrap>;

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
  
  flex-grow: <number>; /* 拉伸因子(grow factor) */
  flex-shrink: <number>;
  flex-basis: <width>;

  flex: none | <flex-grow> (<flex-shrink> <flex-basis>);
  
  align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
```

```html
<style>
  .yellow { order: 1; }
</style>

<div class="yellow"></div>
<div class="green"></div>
<div class="yellow"></div>
<div class="green"></div>
<div class="green"></div>
```

## grid

```css
.row {
  display: flex;
  flex-wrap: wrap;
}
.row > .col, [class^=col-] {
  box-sizing: border-box;
}
.col {
  flex-basis: 0;
  flex-grow: 1;
}
.col-6 {
  flex: 0 0 50%;
}
.col-12 {
  flex: 0 0 100%;
}
@media screen and (max-width: 768px) {
  .col-md-8 {
    flex: 0 0 66.666667%;
  }
  .offset-md-4 {
    margin-left: 33.333333%;
  }
}
```

```scss
$grid-breakpoints: (
  // Extra small screen / phone
  xs: 0,
  // Small screen / phone
  sm: 576px,
  // Medium screen / tablet
  md: 768px,
  // Large screen / desktop
  lg: 992px,
  // Extra large screen / wide desktop
  xl: 1200px
);

$container-max-widths: (
  sm: 540px,
  md: 720px,
  lg: 960px,
  xl: 1140px
);
```

