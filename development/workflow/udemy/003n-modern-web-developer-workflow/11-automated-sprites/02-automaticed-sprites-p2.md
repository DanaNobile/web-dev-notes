# Automated Sprites Part 2
* We will remove the images of our icons
* And replace it and all the others
    - With their corresponding sprite image

## Import auto-generated `templates/sprite.css`
* Instead of doing this:

`styles.css`

```css
// more code
@import 'modules/_testimonial';
@import '/app/temp/sprite/css/sprite.css';
```

# We will use gulp
* To automatically move into our `modules` folder
* That way all of our CSS will live in one **centralized** folder
* And we can import it into the `styles.css` file the same way we did for all the other CSS files

### Remove this line from `styles.css`
`@import '/app/temp/sprite/css/sprite.css';`

* We won't be using it

### Add a new task
* This task will simply move our generated `sprite.css` file into the `modules` folder

`/gulp/tasks/sprites.js`

```js
// more code

gulp.task('copySpriteCSS', function() {
  return gulp.src('./app/temp/sprite/css/*.css')
    .pipe(gulp.dest('./app/assets/styles/modules'));
});
```

* Run this new task

`$ gulp copySpriteCSS`

* Check our `modules` folder and you'll see our generated `sprite.css` has been copied into that folder

## Import our new `sprite.css` file

```css
// more code
@import 'modules/_testimonial';
@import 'modules/sprite.css';
```

* But we also want to rename it so that it begins with an `_` (_underscore_)
* So it matches the others and we stay organized
* We can leverage a package off of `npm` called `gulp-rename`

### Install `gulp-rename`
`$ npm i gulp-rename -D`

### Import gulp-rename and update our task
`sprites.js`

```js
var gulp = require('gulp'),
svgSprite = require('gulp-svg-sprite'), // don't forget to add this comma!
rename = require('gulp-rename'); // add this line

// more code
gulp.task('copySpriteCSS', function() {
  return gulp.src('./app/temp/sprite/css/*.css')
    .pipe(rename('_sprite.css'))
    .pipe(gulp.dest('./app/assets/styles/modules'));
});
```

* Run it again

`$ gulp copySpriteCSS`

## Import our new `sprite.css` file

```css
// more code
@import 'modules/_testimonial';
@import 'modules/_sprite.css';
```

* Delete the older `modules/sprite.css` as we now have the same file but with an underscore

### Replace icons in `index.html`
* Search for `star.svg`
* We will delete the `img` but we need to **remember it's class name**
    - Because we are positioning this icon slightly differently for small screens
* We will change this code:

```html
<h2 class="section-title">
    <img class="section-title__icon" src="assets/images/icons/star.svg">
    Our <strong>Features</strong>
</h2>
```

* To this code

```html
<h2 class="section-title">
    <span class="icon--star section-title__icon"></span>
    Our <strong>Features</strong>
</h2>
```

* We use the `icon--star` class name because this is what we auto-generated

## Troubleshoot
* If you followed all the steps and you still don't see the star icon, then (make sure you are watching with `$ gulp watch`) just resave `_sprite.css` and that will make sure the necessary CSS is imported into our final bundle of CSS

`_sprite.css`

```css
.icon--star {
  width: 64px;
  height: 64px;
  background-image: url('/temp/sprite/css/svg/sprite.css-69f19c2e.svg');
  background-position: 59.11330049261084% 99.56481481481481%;
}
```

* Do that for all the icons
    - rain
    - wifi
    - globe
    - fire
    - comment

## Automate Gulp more
* We may add more sprites
* Currently for every new sprite we'd need to:
    1. run `createSprite`
    2. And then run `copySpriteCSS`
    3. Every time!
* We'll modify this so we only need to run one task
* We do this by created a new task that runs both of those tasks

`sprites.js`

```js
// more code
gulp.task('createSprite', function() {
  return gulp.src('./app/assets/images/icons/**/*.svg')
    .pipe(svgSprite(config))
    .pipe(gulp.dest('./app/temp/sprite/'));
});

gulp.task('copySpriteCSS', function() {
  return gulp.src('./app/temp/sprite/css/*.css')
    .pipe(rename('_sprite.css'))
    .pipe(gulp.dest('./app/assets/styles/modules'));
});

gulp.task('icons', ['createSprite', 'copySpriteCSS']); // add this new task
```

## The icons task
* We just give the task a **name** (_icons_) and told it what to run when the name is called
    - We put all the tasks we want to run inside an **array**

### Houston We Have a Problem!
* But we will have a **problem**, `they will both run at the same time`
* We need the tasks to run one after the other
    - Only run the `copySpriteCSS` task only when the `createSprite` task is fully completed

### Dependencies
To make this work we need to tell Gulp that `copySpriteCSS` depends on `createSprite`

`sprites.js`

```js
gulp.task('copySpriteCSS', ['createSprite'], function() {
  return gulp.src('./app/temp/sprite/css/*.css')
    .pipe(rename('_sprite.css'))
    .pipe(gulp.dest('./app/assets/styles/modules'));
});

gulp.task('icons', ['createSprite', 'copySpriteCSS']);
```

* We made this one slight modification

`gulp.task('copySpriteCSS', ['createSprite'], function() {`

* That is how we tell Gulp about dependencies and now the `copySpriteCSS` task will only run when the `createSprite` task is completed

### Test it out
`/gulp/templates/sprite.css`

* We'll ass a simple comment at the top

```
/* This is just a test */
{{#shapes}}
  .icon--{{base}} {
    width: {{width.outer}}px;
    height: {{height.outer}}px;
    background-image: url('/temp/sprite/css/{{{sprite}}}');
    background-position: {{position.relative.xy}};
  }
{{/shapes}}
```

* Run `$ gulp icons`
* You should see this:

![alls tasks run](https://i.imgur.com/3OmzDtL.png)

* We can see that our tasks are working correctly and the order they were run and how long they took to run
* It waits until `createSprite` was finished before running the `copySpriteCSS` task

### Optimizing
* Open up our generated `sprite.css` file
* Our background-image line is repeated 12 times
* We can automate this

`sprite.css`

```
{{#shapes}}
  {{#first}}
    .icon {
      background-image: url('/temp/sprite/css/{{{sprite}}}');
    }
  {{/first}}

  .icon--{{base}} {
    width: {{width.outer}}px;
    height: {{height.outer}}px;
    background-position: {{position.relative.xy}};
  }
{{/shapes}}
```

* `{{#first}}` - This will only be run once

`$ gulp icons`

## Here is the output
`sprite.css` (generated)

```
    .icon {
      background-image: url('/temp/sprite/css/svg/sprite.css-69f19c2e.svg');
    }

  .icon--clear-view-escapes {
    width: 142.4px;
    height: 59.53px;
    background-position: 0 0;
  }
// more code
```

## Update `index.html`
* We now need to alter our classes to include the new `icon` class

## Bust the cache!
* If you test, you may still see the icons because they are cached
    - See if you can figure out how to clear the cache so that you are not seeing the icons

## Get the icons working again
```html
<span class="icon icon--star section-title__icon"></span>
```

* Just add `icon` at the beginning of the class list for all our elements that need icons

## Add helpful comment
`/gulp/templates/sprite.css`

```
/* Do not edit modules/_sprite.css directly as it is generated automatically by Gulp
Instead edit gulp/templates/sprite.css */

{{#shapes}}
// more code
```

* This comment may save a team member from editing the wrong file

## Alter filename
`background-image: url('/temp/sprite/css/svg/sprite.css-69f19c2e.svg');`

* Let's remove the `.css` from the file name
* We do this by altering our `sprite.js`

![alter config file](https://i.imgur.com/dlFShSz.png)

`sprite.js`

```
// more code
var config = {
  mode: {
    css: {
      sprite: 'svg/sprite.svg', // add this line
      render: {
        css: {
          template: './gulp/templates/sprite.css'
        }
      }
    }
  }
}
// more code
```

`$ gulp icons`

* And now `sprite.css` looks like this

```css
.icon {
  background-image: url('/temp/sprite/css/svg/sprite-69f19c2e.svg');
}
```

* no more `.css`!

## Let's move our generated sprite
* Into our main `images` folder
* This will keep our site organized

`sprites.js`

```js
// more code
gulp.task('createSprite', function() {
  return gulp.src('./app/assets/images/icons/**/*.svg')
    .pipe(svgSprite(config))
    .pipe(gulp.dest('./app/temp/sprite/'));
});

// add this task
gulp.task('copySpriteGraphic', function() {
  return gulp.src('./app/temp/sprite/css/**/*.svg')
    .pipe(gulp.dest('./app/assets/images/sprites'));
});
// more code
```

* We need to update our path too

`sprite.css`

* Change this:
 
```
{{#first}}
    .icon {
      background-image: url('/temp/sprite/css/{{{sprite}}}');
    }
{{/first}}
```

To this:

```
{{#first}}
  .icon {
    background-image: url('/assets/images/sprites/{{{sprite}}}');
  }
{{/first}}
```

* We don't need to put it inside a `svg` folder

`sprites.js`

```
// more code
var config = {
  mode: {
    css: {
      sprite: 'svg/sprite.svg',
```

To this:

```
// more code
var config = {
  mode: {
    css: {
      sprite: 'sprite.svg',
```

* We want our `copySpriteGraphic` task to consider `createSprite` task as a dependency

`sprites.js`

```js
// more code
gulp.task('copySpriteGraphic', ['createSprite'], function() {
  return gulp.src('./app/temp/sprite/css/**/*.svg')
    .pipe(gulp.dest('./app/assets/images/sprites'));
});
// more code
```

* And we want to add the `copySpriteGraphic` task to our main group inside `icons` task

`sprites.js`

```js
gulp.task('icons', ['createSprite', 'copySpriteGraphic', 'copySpriteCSS']);
```

* Delete `sprite` folder inside `/app/temp`

`$ gulp icons`

* See if `sprite` folder was regenerated

![regenerated sprite folder](https://i.imgur.com/lIkxS4Y.png)

`$ gulp watch`

## Test in the browser and inspect star icon
* You should see new path

![new star path](https://i.imgur.com/o0FTIcC.png)

