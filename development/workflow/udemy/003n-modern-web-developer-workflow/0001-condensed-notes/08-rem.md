## REM
* All of the following should use REM (instead of `px`, `em`)
  - Padding
  - Margin
  - Font-Size

### What is REM?
* When we use `rem` everything is relative to the root of the page
* The root of every **HTML** page is `html`
* Most browsers have a default size of `html` of `16px`
  - So if you want your font to be `24px`
      + `16px * 1.5rem = 24px`

### Why use REM?
* Not everyone has their web browser configured the same
* Other people may change their default `html` font size to deal with stuff like:
    - eye problems
    - near-sighted
    - far-sighted 
    - or just avoiding eye-strain
        + (_they may have their `HTML` configured to use `30px`_)

##### As a good web developer we want to honor the user's font size preference
* If we use REM for `font-size`, `padding` and `margin` our entire web site, all the **white-space** and balance will scale accordingly to the user's font-size preference
  - If we used `px` our **font-size** would be set in stone
  - If the user had a larger **font-size**, the text might appear too big for the layout surrounding it

### How can we eliminate guesswork with font-sizes?
`_large-hero.css`

```css
&__description {
  color: #fff;
  font-size: 1.875rem; /* 30 / 16 = 1.875rem */
  font-weight: 100;
  text-shadow: 2px 2px 0 rgba(0, 0, 0, .1);
  max-width: 30rem; /* 480 / 16 = 30rem */
  margin-left: auto;
  margin-right: auto;
}
```

* Instead of tweaking and tweaking numbers, is their a faster way to find out what our font size should be?
* Open the photoshop file for measurements
