# Working with React Events
Events in React are same as events in JavaScript

## [SyntheticEvent](https://facebook.github.io/react/docs/events.html)

The only difference is all React events are wrapped in the cross-browser `SyntheticEvent`. The only reason for this is to make sure it works across all browsers

## inline JavaScript? WTF???
Yes, in React, you write your events inline. You probably have been taught that the separation of concerns is good (put html in html, css in css and JavaScript in JavaScript). In React, they all come back together and go against what you were taught... but for a good reason. It makes sense.

## Our first event
We need to do **2** things:

1. When the user submits the TeamPicker's button we need to grab the value the user entered into the input field and get that value into React
2. We need to change the URL from `/` to `/team/value-they-type-in-input-field`

### Which event should we use?
We need `click` but also if someone hits the `return/enter` key and that event is the `onSubmit` event

#### Creating our own method
What do we want to happen when the `onSubmit` is fired? We will create a function above the `render()` that will have what we want to happen coded inside that custom function. We will then call that function by name at the value for our `onSubmit()` event

**important** ES6 classes DO NOT have any commas inside them

`src/components/TeamPicker.js`

```
class TeamPicker extends React.Component {
  goToTeam() {
    console.log('goToTeam() method fired!');
    // first grab text from text field
    // second change URL from / to /team/:teamId
  }

  render() {
    return (
      <form className="team-selector" onSubmit={this.goToTeam}>
        {/* Look here */}
        <h2>Please Enter a Team</h2>
        <input type="text" required placeholder="Team Name" defaultValue={getFunName()} />
        <button type="submit">Visit Team</button>
      </form>
    )
  }
}

export default TeamPicker;
```

### We use `this.goToTeam` and not just `goToTeam`
Why `this`?
Because `render()` methods are bound to the class they are inside of

### Bind or not to Bind
But all the other methods that we create will not be bound to the class so we need to use `this` to associate that method with the class

### How can we bind these methods to the component (class)? 
There is a way that we will talk about later

### Using `this`
Because `render()` is bound to the Component, we can use `this` inside the `render()` method and it will always equal the Component

### Flash in the pan
Why when we `press enter` or `click` the button does the `console.log()` first and then disappear?

Because the page refreshes

**note** We do not use `goToTeam()` but rather `goToTeam` because we don't want to fire the event as soon as the page loads, so we omit the `()` and just reference the function name and wait for the user to `click` submit button or `press enter` before we call the function and trigger the `console.log()`

## Preserve the log
To preserve the log and prevent it from quickly disappearing we can click the `Preserve log` checkbox in the inspect. Check it and click the button

You will see:

![Preserve log](https://i.imgur.com/6i4Z0f4.png)

## Why is `console.log()` flashing and leaving?
Forms by default take the data you enter into them and then it will send that data to whatever the action of the form is (or it will just refresh the page if we didn't provide an action

### preventDefault()
In vanilla JavaScript we just use `preventDefault()` to stop a form from submitting or refreshing the page

#### Update our `goToTeam()` method
```js
goToTeam(e) {
    e.preventDefault();
    console.log('goToTeam() method fired!');
    // first grab text from text field
    // second change URL from / to /team/:teamId
}
```

* `e` is the event that we are now passing to our `goToTeam()` method
* `e.preventDefault()` prevents the form from refreshing the page
* You can uncheck the `Preserve log` checkbox
* View the console tab and you will see every time you click or press enter or goToTeam() method is fired and the default page refresh on submit does not happen because we turned off the default JavaScript form behavior

## Can't Touch This... The DOM that is
### Grabbing the input text value
We can't use jQuery with something like `const value = $('input').val();` but in React you want to avoid touching the DOM as much as possible because the way React works is we modify the data and we write our JSX and we are hands off with touching the DOM. We let React handle handling the DOM for us

### So if we can't touch the DOM how do we get stuff from the DOM when we need it?
Using a `ref`

#### What is a `ref`?
A way to reference the actual inputs

**note** React phased out `string refs` and replaced with `function refs`

```
// This is a string ref
render(
 return (
  <input ref="teamInput" />
 )
)

// This is a functional ref
render(
 return (
   <input type="text" required placeholder="Team Name" defaultValue={getFunName()} ref={(input) => { this.teamInput = input }} />
 )
)
```

So what the above code is doing is when the input is added to the page, it will have a reference added to it on the class itself

## Seeing is Believing
So check out **React tab**. You will see that it takes a lot of clicking to get to our `TeamPicker` because now that we are using the `router` Component, it has added a ton of nested Components

![long path to TeamPicker](https://i.imgur.com/T07GSBa.png)

Notice that when we select the `input` we see it has a `Ref` on it

### Search is faster
Instead of digging deep to find the `TeamPicker` Component you can just search for it inside the `Search by Component Name` text field (on **React tab**) and type `TeamPicker`

## To Bind or not to bind
We can use `this` inside `render()` but outside of `render()` this does not equal the Component

### How do I reference the TeamPicker inside of another method?
Old way

```
var TeamPicker = React.createClass({
    goToTeam() {
        // `this` would work here because this would always equal the Component itself
        console.log(this);
    }
    render() {
        return <p>yo</p>
    }
});
```

But **React** now switched to using ES6 class and the binding of `this` has changed. Why?

Because ES6 classes do not implicitly bind all the methods to the class (Component)

### Two ways to manually bind `this` to our Component
#### 1. Use the constructor of the component

```
class TeamPicker extends React.Component {
  constructor() {
    super();
    this.goToTeam = this.goToTeam.bind(this);
  }
  goToTeam(e) {
    e.preventDefault();
    console.log('goToTeam() method fired!');
    // first grab text from text field
    console.log(this.teamInput );
    // second change URL from / to /team/:teamId
  }

  render() {
    return (
      <form className="team-selector" onSubmit={this.goToTeam}>
        {/* Look here */}
        <h2>Please Enter a Team</h2>
        <input type="text" required placeholder="Team Name" defaultValue={getFunName()} ref="{(input) => { this.teamInput = input }}"/>
        <button type="submit">Visit Team</button>
      </form>
    )
  }
}

export default TeamPicker;
```

**note** A common problem is if you surround the `ref` attribute values with double quotes like

```
<input type="text" required placeholder="Team Name" defaultValue={getFunName()} ref="{(input) => { this.teamInput = input }}"/>
```

Instead of using the above code... use the below code instead

```
<input type="text" required placeholder="Team Name" defaultValue={getFunName()} ref={(input) => { this.teamInput = input }} />
```

* We use the `constructor()` method to that will take all the methods from the parent class (`React.Component`)
    - `super()` allows us to create a new instance of `React.Component` and name it StoryPicker and then sprinkle onto it our own methods like `goToTeam`
        + Then we can, through the constructor, specifically bind `this.goToTeam` by assigning it `this.goToTeam.bind(this);`
        + This is strange for people to wrap their head around. Knowing how ES6 classes work will help but you then have to do this for every method you want to bind to this
            * React developers use this for methods they use more than once

**note** Comment out the constructor code

## 2. `{this.goToTeam.bind(this)}`

```
import React from 'react';
import { getFunName } from '../helpers';

class TeamPicker extends React.Component {
  // constructor() {
  //   super();
  //   this.goToTeam = this.goToTeam.bind(this);
  // }
  goToTeam(e) {
    e.preventDefault();
    console.log('goToTeam() method fired!');
    // first grab text from text field
    console.log(this.teamInput );
    // second change URL from / to /team/:teamId
  }

  render() {
    return (
      <form className="team-selector" onSubmit={this.goToTeam.bind(this)}>
        {/* Look here */}
        <h2>Please Enter a Team</h2>
        <input type="text" required placeholder="Team Name" defaultValue={getFunName()} ref={(input) => { this.teamInput = input }} />

        <button type="submit">Visit Team</button>
      </form>
    )
  }
}

export default TeamPicker;
```

### View in browser and you will see clicking the button returns the `input`

If you change `console.log(this.teamInput )` to `console.log(this)` you will see the `TeamPicker` Component

## Binding with the Arrow function (The ES6 way)
Binding the ES6 way with a fat arrow

Change this:

`onSubmit={this.goToTeam.bind(this)}`

To this:

`onSubmit={(e) => this.goToTeam(e)}`

**note** When we bind inline we are essentially creating a function for every single Component that gets rendered but if you do it the constructor way it is all referring to the single `goToTeam()` method. So the bottom line is if you have lots of binding in a Component, do not do it inline, do it inside the constructor

Now grab the value with:

`console.log(this.teamInput.value )`

## View in console. 
Click button and you will see the text in the console with what text is inside the input text field

### I like binding within the class as I find it more readable

```
import React from 'react';
import { getFunName } from '../helpers';

class TeamPicker extends React.Component {
  constructor() {
    super();
    this.goToTeam = this.goToTeam.bind(this);
  }
  goToTeam(e) {
    e.preventDefault();
    // first grab text from text field
    console.log(this.teamInput.value );
    // second change URL from / to /team/:teamId
  }

  render() {
    return (
      <form className="team-selector" onSubmit={this.goToTeam}>
        {/* Look here */}
        <h2>Please Enter a Team</h2>
        <input type="text" required placeholder="Team Name" defaultValue={getFunName()} ref={(input) => { this.teamInput = input }} />

        <button type="submit">Visit Team</button>
      </form>
    )
  }
}

export default TeamPicker;
```

## What's next?
How can we get React Router to take us from `/` to `/team/:teamId` when the form is submitted?
