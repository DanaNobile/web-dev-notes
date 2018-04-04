# Our first Component
## The TeamPicker
* You can type in the name of a team or it autofills with one
* And there will be a button that when you click it, it will take you to that individual team

## Getting started
* We'll start in `index.js` and eventually move to the `Components` folder

## Loading React
* Do we use a script tag inside `index.html`?
    No
* Modern JavaScript has evolved to using ES6 modules

## ES6 Modules
`$ yarn add react`

* That will save it into our `package.json`

### Yarn vs Npm
[article explaining the difference between yarn and npm](https://www.keycdn.com/blog/npm-vs-yarn/)

### What is npx
[article explaining npx](https://medium.com/@maybekatz/introducing-npx-an-npm-package-runner-55f7d4bd282b)

* If you don't have a `package.json` yet, first create it with:

`$ yarn init -y`

* The -y prevents you get several questions and just accepts the default values to quickly create your `package.json`

* At the top of `index.js` add:

`import React from 'react';`

* That will import the React library
    - We downloaded into `node_modules` into the **React** variable

## Run app, view in browser

## eslint Warnings in the inspector 'Console' tab
![eslint warning](https://i.imgur.com/Ij15mCV.png)

* These pop up to let you know stuff you should think about fixing in your JavaScript

**note** [Create React App](https://github.com/facebookincubator/create-react-app) - comes with minimal eslint rules

### Ejected!
* We can **eject** out of that
* And use our own `eslint` rules if we want

## View the `Elements` tab of the inspector
* A bundled script has been added
    - If you [view that in the browser](http://localhost:3000/static/js/bundle.js), all that code is **React**
* If you comment out our **React** import all the **React** code will be removed
  - Try it and you still will see a ton of code
  - That is just `webpack` code that is just for development and will be stripped out for production
  - If you add `alert('yo');` into `index.js` and check for it, you will see where it gets added

## Add a jsx comment `{ /* comment */ }`
`index.js`

```
// MORE CODE
class TeamPicker extends React.Component {
  render() {
    return (
      <form  className="team-selector">
        { /* comment */ }
        <h2>Pick a team</h2>
      </form>
    )
  }
}
// MORE CODE
```
* **note** Comments have to be inside a parent element
    - You will get an error if they are not and it is an error that is misleading so be careful
* Remove the `alert('yo');`
* Also remove the comment in our **React** import

### Components are always Capitalized!
**rule** - Components are always Capitalized
* Great because they can be used more than once and seeing the capital letter reminds you the special purpose of this Component

### The `render()` method is required for all Components!
* **rule** - Every Component needs at least one method and that is the `render()` method
* When a Component gets rendered to a page, it will ask that Component what HTML should I display?

`index.js`

```
import React from 'react';

class TeamPicker extends React.Component {
  render() {
    return <p>Hello from the Team Picker Component</p>
  }
}
```

* Old way of writing methods in classes

```js
// old way of writing methods in classes
function render() {

}
// ES6 way of writing methods in classes
render() {

}
```

## Where will our Component HTML code go?
* To the mounting point

### What is the mounting point?
In `public/index.html` this is our mounting point

```html
<div id="main">
  <!-- Main React app goes here -->
</div>
```

## ReactDOM
* React is not just for the HTML and the DOM it can also be used for:
    - Android apps
    - IOS apps
    - Canvas rendor
* But we want it to render out to HTML so we need to install and import ReactDOM

### Install ReactDOM
`$ yarn add react-dom`

### Import ReactDOM
`index.js`

```js
import React from 'react';
import { render } from 'react-dom'; // add this line
```

#### Two ways to import
```js
import { render } from 'react-dom';
//and
import ReactDOM from 'react-dom';
ReactDOM.render();
```

## Named exports
* But we use the first import case because we don't need the entire ReactDOM package, we just need one `render()` method of ReactDom

#### { method }
* Use this syntax when just importing one method from a Package
* This is ES6 syntax

## Add class
```
import React from 'react';
import { render } from 'react-dom';

class TeamPicker extends React.Component {
  render() {
    return <p>Hello from the Team Picker Component</p>
  }
}
```

## `render()` it

`render(What component would we like to render?, What element should it render out?)`

* Add this code to the bottom of `index.js`:

`render(<TeamPicker/>, document.querySelector('#main'));`

**note** `<TeamPicker/>` is `JSX`

## Browser Updates
* You will see `Hello from the TeamPicker Component`

### React Dev Tools
* Shows the `<TeamPicker>` Component

## Moving our Component to it's own file
* Create a new file called `Components/TeamPicker.js`
* Copy class from `index.js` to this new file

## BEST PRACTICE - Components in separate file
* Put each of your Components into a separate file
* The main benefit of this best practice is it makes our code much easier to maintain

`Components/TeamPicker.js`

```
class TeamPicker extends React.Component {
  render() {
    return <p>Hello from the TeamPicker component</p>
  }
}
```

### Are we done?
* Nope
* What Else do we need to do?

#### Import React, export module and import Component
* To the file that will be using it

##### Import React
* We need to import **React**
* You will have to do this on top of every single Component
    - **React** is not like **jQuery** where you load on page and it is available to all
* With ES6 Modules, if you need something inside a `js` module you need to import it inside every single file that needs it 

```
import React from 'react'; // add this line

class TeamPicker extends React.Component {
  render() {
    return <p>Hello from the TeamPicker component</p>
  }
}
```
## Point out difference in comments
* pure javascript comments vs JSX comments

##### Export module
```
import React from 'react';

class TeamPicker extends React.Component {
  render() {
    return <p>Hello from the TeamPicker component</p>
  }
}

export default TeamPicker; // Add this line
```

##### Import Component to file that will be using it
```
import React from 'react';
import { render } from 'react-dom';

import TeamPicker from './components/TeamPicker'; // add this line

render(<TeamPicker/>, document.querySelector('#main'));
```

**note** 

* Path is important here
* You need to point where the Component is relative to the file you are importing it into
* If our import is just a string `TeamPicker`, **webpack** thinks it is inside the `node_modules` directory
* `TeamPicker` is not of `node_modules` it is a module that we created so with custom Components **we need to supply a relative path**

## Rule
* You do not to append the `.js` suffix
*   It is assumed so you don't need to add it and it may also cause an error so don't add `.js` extensions

`TeamPicker.js`

```
import React from 'react';

class TeamPicker extends React.Component {
  render() {
    return (
      <form  className="store-selector">
        <h2>Pick a Team</h2>
        <input type="text" required placeholder="Team Name"
        <button type="submit">Visit Team</button>
      </form>
    )
  }
}

export default TeamPicker;
```

![team picker form](https://i.imgur.com/TXo1Nwl.png)

* Form doesn't do anything when submitted... but we'll fix that next
