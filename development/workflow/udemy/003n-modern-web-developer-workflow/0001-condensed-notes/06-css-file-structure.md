# Agile Development and Scrum

# CSS File Structure and other cool Stuff
1. File architecture
    * Organize CSS into multiple files
    * Each file to be relatively small and have a specific purpose
2. Identify patterns in design
3. Rules to follow for creating class names and writing our CSS selectors

## Starter page
* Travel site
* Open `index.html` in browser

![starter file](https://i.imgur.com/a9r9qkk.png)

### Begin coding
* Clear all code in `styles.css`

`app/assets/styles/styles.css`

```css
body {
  font-family: 'Roboto', sans-serif;
  color: #333;
}
```

## Start watching with Gulp
`$ gulp watch`

* Make a small change to regenerate our gulp code

### Fix image
* It is so large that it is horizontally scrolling

`styles.css`

```css
// more code
img {
  max-width: 100%;
  height: auto;
}
```

* We took care of global styles that we want to trickle down to everything

## Now we start focusing on unique pieces
### Start with HERO area
* skip navigation for now

`index.html`

```html
// more code
 <div class="large-hero">
       <div class="large-hero__text-content">
         <img src="assets/images/hero--large.jpg">
         <h1>Retail Apocalypse</h1>
         <h2>Stores are closing</h2>
         <p>Let us know if a store is closing near you</p>
         <p><a href="#">Register Now</a></p>
       </div>
   </div>
// more code
```

### Modular code
* We don't want all our CSS in one file
* Create `/app/assets/styles/modules/_large-hero.css`

`_large-hero.css`

```css
.large-hero {
  position: relative;
}
```

#### Partials
* Why `_` before file name (_underscore_)?
  - Traditional naming convention to your team that this file is a partial

#### Import partial
`styles.css`

```
@import 'modules/_large-hero';
// more code
```

* `imports` MUST be at very top of file
* `.css` extensions not needed (example: `_large-hero.css`)
* `@import` is a native CSS feature
    - But we DO NOT want the browser to have to download multiple CSS files
    - We will tell Gulp and PostCSS to look for this line and replace it with the contents of the `_large-hero.css` file

### Stop Gulp!
* By typing this in the Terminal:  

`ctrl` + `c`

### Install postcss-import
`$ npm i postcss-import -D`

### Import and use it
`gulpfile.js`

```js
// more code
cssImport = require('postcss-import'), // add this line
watch = require('gulp-watch');
// more code
gulp.task('styles', function() {
  return gulp.src('./app/assets/styles/styles.css')
      // add cssImport like below
      .pipe(postcss([cssImport, cssvars, nested, autoprefixer]))
      .pipe(gulp.dest('./app/temp/styles'));
});
// more code
```

* Make sure to put `cssImport` at very beginning of array

`$ gulp watch`

* View generated CSS inside `temp` folder and if you see `.large-hero` selector at top, our import of the partial was successful

## Create `/assets/styles/base`
* We will put all of our **global CSS** inside `base` folder
* We'll cut and past our gobal code into a new file

`/assets/styles/base/_global.css`

```css
body {
  font-family: 'Roboto', sans-serif;
  color: #333;
}

img {
  max-width: 100%;
  height: auto;
}
```

And modify:

`styles.css`

```css
@import "base/_global";
@import "modules/_large-hero";
```

### Our very own CSS Recipe book
* Our main CSS file doesn't contain any CSS itself
* It is like a recipe pointing to other recipes
    - Where each ingredient has its own specific focused purpose
    - Structuring it like this will help keep our code awesomely organized

### We need to add `normalize.css`
* How do we grab it from `node_modules`?
* PostCSS makes this easy!

`styles.css`

```css
@import "normalize.css";
@import "base/_global";
@import "modules/_large-hero";
```

* View your output CSS file and you'll see we have imported normalize!
* You need the the `.css` extension on normalize or you will get an error

![normalize effect](https://i.imgur.com/5P3WFxT.png)

* We remove that white border

### What does normalize do?
* `Normalize.css` is an alternative to other CSS resets, it adjusts the styles of certain elements in an effort to make all web browsers render things more consistently
* You should add `normalize.css` at the very beginning of **ALL** your projects
* [Read More](http://nicolasgallagher.com/about-normalize-css/)

### Back to here
* Position text to sit on top of image

![wrap text](https://i.imgur.com/ngb1Gdy.png)

* We'll wrap this text it its own `div` so we can move position all the text as one cohesive unit
    - The alternative would be to position each element independently, which would be a major time suck

### Add a div container and some BEM
`index.html`

```html
// more code
<div class="large-hero">
      <div class="large-hero__text-content">
        <img src="assets/images/hero--large.jpg">
        <h1>Retail Apocalypse</h1>
        <h2>Stores are closing</h2>
        <p>Let us know if a store is closing near you</p>
        <p><a href="#">Register Now</a></p>
      </div>
  </div>
// more code
```

### Why are we using `large-hero__text-content` as our name?
    - BEM - bigger discussion, coming later

### Add the CSS
* Center content vertically and horizontally
    - Developers handle this various ways
    - Here is one way
    - Flex is another option that would make this easier
      + Watch all [Wes Bos free flex videos](https://flexbox.io/) to learn more

`_large-hero.css`

```css
.large-hero {
  position: relative;
}

.large-hero__text-content {
  position: absolute;
  top: 50%;
  left: 0;

  text-align: center;
  transform: translateY(-50%);
  width: 100%;
}
```

## What is `positioning`?
absolute vs relative

### Tip: Organize properties alphabetically

### Tip: Put position properties up top and separate them from the other properties

### `translateY()` function
* Enables us to position an element vertically relative to itself
* If we use `translateY(100%)` it will put the content down the height of the element
* `translateY(0)` is default
* We just want to pull it up half of it's own height so we use `translateY(-50%)`

### Git Stuff
* In a new Terminal tab (make sure you are in project root)
* `$ git status`
* Add to staging `$ git add -A`
* Commit snapshot `$ git commit -m 'Add BEM and hero stuff'`
* Checkout to master `$ git checkout master`
* Merge Branch to master
* `$ git merge NAMEOFYOURBRANCH`

## Next - Responsive Images and BEM
* Why did we put the huge image on the page versus making it a background image?
* Both ways are acceptable
* Next we will be learning about responsive images and this will show us how we can make using this large image on our page acceptable
