# Reveal Elements on Scroll
* How to reveal an element when it has been scrolled to
* This loads items when you need them and saves you from having to download the whole site at one time

## Git Stuff
`$ git status`

* Add all changes with:

`$ git all -A`

* Commit changes

`$ git commit -m 'Complete desktop and mobile header styles`

* Merge branch into master

`$ git checkout master`

`$ git merge header`

`$ git push origin master`

## New Branch
`$ git checkout -b reveal-on-scroll`

### New Module
`/app/assets/scripts/modules/RevealOnScroll.js`

```js
class RevealOnScroll {

}

export default RevealOnScroll;
```

### Import it within our main JavaScript file
`App.js`

```js
import MobileMenu from './modules/MobileMenu';
import RevealOnScroll from './modules/RevealOnScroll'; // add this line

const mobileMenu = new MobileMenu();
const revealOnScroll = new RevealOnScroll(); // add this line
```

`RevealOnScroll.js`

* We will log out all the divs that have a feature-item class
* Then we'll add a class called 'reveal-item' to each of them
* View the feature-items array inside the Google console
* And see how it changes with `this.hideInitially()` commented out
* Comment it back in when you are finished and see what it looks like then

### Commented Out
![commented out](https://i.imgur.com/4t5JznB.png)

### Commented in
![shows reveal-item](https://i.imgur.com/4S5w9kr.png)

```js
import $ from 'jquery';

class RevealOnScroll {
  constructor() {
    this.itemsToReveal = $('.feature-item');
    console.log(this.itemsToReveal);
    this.hideInitially();
  }

  hideInitially() {
    this.itemsToReveal.addClass('reveal-item');
  }
}

export default RevealOnScroll;
```

`/app/assets/styles/modules/_reveal-item.css`a

* We hide all items that have a class of `reveal-item`
* We will fade them out slowly
* And when a class of `reveal-item--is-visible` is added
    - We'll make them fully opaque (visible)

```css
.reveal-item {
 opacity: 0;
 transition: opacity 1.5s ease-out;

 &--is-visible {
  opacity: 1;
 }
}
```

## Import the CSS file into our main CSS file
`styles.css`

```css
// more code
@import "modules/_reveal-item";
```

### WayPoints
We could right the reveal to scroll code ourselves but instead we will leaverage WayPoints

#### Install waypoints
`$ npm i waypoints -S`

* Waypoints doesn't have a main file
    - This is kind of a bummer because when we import waypoints it will have super long path like this
    - `../../../../node_modules/waypoints/lib/noframework.waypoints`
* We have to point to the file we want to use
* `this.itemsToReveal` is storing a jQuery collection of the four feature items
    - `.each` is a jQuery function that enables us to loop over a collection

![holding a collection](https://i.imgur.com/mTaXFvq.png)

`RevealOnScroll.js`

```js
import $ from 'jquery';
import waypoints from '../../../../node_modules/waypoints/lib/noframework.waypoints';

class RevealOnScroll {
  constructor() {
    this.itemsToReveal = $('.feature-item');
    // console.log(this.itemsToReveal);
    this.hideInitially();
    this.createWaypoints();
  }

  hideInitially() {
    this.itemsToReveal.addClass('reveal-item');
  }

  createWaypoints() {
    this.itemsToReveal.each(function() {
      console.log('waypoints!');
    });
  }
}

export default RevealOnScroll;
```

## Test it out in the browser
* You will see `4 waypoints` in console

![4 waypoints](https://i.imgur.com/RUNikmX.png)

`RevealOnScroll.js`

```js
import $ from 'jquery';
import waypoints from '../../../../node_modules/waypoints/lib/noframework.waypoints';

class RevealOnScroll {
  constructor() {
    this.itemsToReveal = $('.feature-item');
    // console.log(this.itemsToReveal);
    this.hideInitially();
    this.createWaypoints();
  }

  hideInitially() {
    this.itemsToReveal.addClass('reveal-item');
  }

  createWaypoints() {
    this.itemsToReveal.each(function() {
      const currentItem = this;
      new Waypoint({
        element: currentItem,
        handler: function() {
          $(currentItem).addClass('reveal-item--is-visible');
        },
        offset: '85%'
      });
    });
  }
}

export default RevealOnScroll;
```

* `element` is the DOM element we want to watch for as we scroll down the page
* `handler` is what we want to happen when that element is scrolled to
* Inside jQuery's `each()` method `this` points to the current DOM element
* But we can't use `this` because `new Waypoint()` is creating a new object and then the `this` keyword will point to the current Waypoint object
* We see the feature boxes fade it but is too late
    - Waypoints by default doesn't trigger it's handler until the very top of the element is waiting for crosses the top of the viewport
    - We add an `offset` key and `'100%'` would the items crossing the bottom of the viewport, we set it to 85% for a better effect

## Next - We will refactor our RevealOnScroll.js
So that it is modular and flexible

