# Loading data into `state`

Update `src/components/Roster.js`

```js
import React from 'react';
import AddPlayerForm from './AddPlayerForm';

class Roster extends React.Component {
  render() {
    return (
      <div>
        <h2>Roster</h2>
        <AddPlayerForm addPlayer={this.props.addPlayer} />
        <button onClick={this.loadSamples}>Load Sample Players</button>
      </div>
    )
  }
}

export default Roster;
```

### Where do we put our `loadSamples()` method?
Not in `Roster.js`. We need to include it where our `state` lives and that lives in our `App` Component

## Open App.js
We need to import our `sample-players.js` file into `App`

`import samplePlayers from '../sample-players';`

### Bind `this`
Inside our `loadSamples()` method we need to bind this to our App Component

```js
constructor() {
    super();
    this.addPlayer = this.addPlayer.bind(this);
    this.loadSamples = this.loadSamples.bind(this); // add this to bind `this`
    this.state = {
      players: {},
      lineup: {}
    };
  }
```

### our `loadSamples()` method

```js
loadSamples() {
    this.setState({
      players: samplePlayers
    });
  }
```

## How do we get the `loadSamples()` method to work?
How can we get it to work on our `load sample players` button inside `Roster.js`

### Update `render()` in `App.js`

```
render() {
    return (
      <div className="team-of-the-day">
        <div className="menu">
          <Header tagline="Great Players" />
        </div>
        <Lineup />
        <Roster addPlayer={this.addPlayer} loadSamples={this.loadSamples}/>
      </div>
    )
  }
```

We just need to give the `Roster` Component our `loadSamples()` method

### Update`render()`

`Roster.js`

```
render() {
    return (
      <div>
        <h2>Roster</h2>
        <AddPlayerForm addPlayer={this.props.addPlayer} />
        <button onClick={this.props.loadSamples}>Load Sample Players</button>
      </div>
    )
  }
```

We gain access to the `loadSamples()` method in our child Component using `props`

### View in browser
1. Click **Load Sample Players** button
2. View **React Tab** and search for `App` Component
3. View `state` and you'll see our 20 sample players have been added

![sample players added](https://i.imgur.com/0pk5zhh.png)


