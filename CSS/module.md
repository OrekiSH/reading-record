## BEM

```css
/* This is a Block */ 
.container {}

/* This is an element that depends upon the block */ 
.container__btn {}

/* And those are modifiers that changes the style of the block */ 
.container--rounded {} 
.container--transparent {}
```

- A block should never override the styles of another block or modifier
- an `.accordion__copy–open` modifier which lets us know we shouldn't use it on another block or element

```css
/* it's not BEM */
.nav .nav__listItem .btn--orange {
  background-color: green;
}

## RSCSS

```scss
/* Components will be named with at least two words, with a dash between each word */
.like-button {}

/* Each component may have elements. They should have classes that are only one word. */
.article-card {
  .title     { /* okay */ }
  > .author  { /* ✓ better */ }
}

/* For those that need two or more words, concatenate them without dashes or underscores. */
.profile-box {
  > .firstname { /* ... */ }
  > .lastname { /* ... */ }
  > .avatar { /* ... */ }
}

/* tag selectors may come at a small performance penalty and may not be as descriptive */
.article-card {
  > h3    { /* ✗ avoid */ }
  > .name { /* ✓ better */ }
}

/* For general-purpose classes meant to override values, put them in a separate file and name them beginning with an underscore. using too many helpers should be discouraged. */
._unmargin { margin: 0 !important; }
._center { text-align: center !important; }
._pull-left { float: left !important; }

/* ✗ Avoid: 3 levels of nesting */
.image-frame {
  > .description {
    /* ... */

    > .icon {
      /* ... */
    }
  }
}

/* ✓ Better: 2 levels */
.image-frame {
  > .description { /* ... */ }
  > .description > .icon { /* ... */ }
}
```
