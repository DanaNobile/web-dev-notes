# Redux Actions
actions and actionCreators - **Fundamental concept behind React**

## What are actions in React?

### Examples:

* Someone clicks on a photo
* Someone loads a page
* Someone likes an image
* Someone deletes a comment
* Someon adds a new comment

When any of the above happens in our app it dispatches an `action`

That dispatched action has two things inside it:

1. The type of action that happened
2. A payload of information that is needed
    * Which comment was deleted?
    * Which photo should be added?
    * What was the comment someone added?
    * Who was the author?

**note** Any information about what specifically happened

## Redux tab
Any time an action is fired the **Redux** tab in the Chrome dev tools will show all information about that action

**time traveling** - You can comment on and off to see what was the `state` before and after the `action`

## Reducers will do the opposite
How do I handle the changes once they happen?

## Let's learn about `actionCreators`
Create a new folder called `actions`

### `actionCreators.js`
Create `client/actions/actionCreators.js`

**note** Sometimes developers create one `actionCreator` per file, but ours will be small and _we'll just put them all in one file_

`actionCreators.js`

```
// increment

// add comment

// remove comment
```

We also will have changing of the `route` but that will happen via **Redux**. That is why we use the `React Router Redux combination`, even when you change a page, that is something that happened in your app and we are storing them in our `store`

## increment()
```
// increment
function increment(index) {
  return {
    type: 'INCREMENT_LIKES',
    index
  }
}
```

We pass the `post` as an **argument** and **return an object** because `actions` are just objects that have two things:

1. The type
2. The index

* We only use `index` because of **ES6** instead of typing `index: index`

## addComment()

```
// add comment
function addComment(postId, author, comment) {
  return {
    type: 'ADD_COMMENT',
    postId,
    author,
    comment
  }
}
```

**super cool atom tip** - `ctrl` + `cmd` + `click` and drag multiple words and you can paste them on another line and you will get to paste them all on their own lines

## removeComment()
```
// remove comment
function removeComment(postId, i) {
  type: 'REMOVE_COMMENT',
  i,
  postId
}
```

## Why do we call them actionCreators?
Inside the functions are the `actions` but the function is what creates the action and then we will **dispatch** it out into the air

## Export all our actionCreators
So we can access them later on from different files

```
// increment
export function increment(index) {
  return {
    type: 'INCREMENT_LIKES',
    index
  }
}
// add comment
export function addComment(postId, author, comment) {
  return {
    type: 'ADD_COMMENT',
    postId,
    author,
    comment
  }
}
// remove comment
export function removeComment(postId, i) {
  type: 'REMOVE_COMMENT',
  i,
  postId
}
```

## Next...
When these `actionCreators` get fired (_the correct word is `dispatched`_), we will soon wire it up to the **UI**

* How are we going to handle that **data**? 
* How are we going to **add** or **remove** that `comment` or **increment** the number of **likes**? 
* How are we going to handle doing anything with our **data** to update our `store`?

**Hint:** We will do that with `Reducers`
