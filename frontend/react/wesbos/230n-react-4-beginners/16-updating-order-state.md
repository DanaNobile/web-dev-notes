# Updating Lineup State

## Get Add to Lineup to work
Our button need to be dynamic. It needs to say `Add to Lineup`, `Injured`, `Out`

`Player.js`

```
const isAvailable = details.status === 'active';
```

We check each of our players to find their **status**. We set variable `isAvailable` to **true** if our player has a status of **"active"**

`Player.js`

`const buttonText = isAvailable ? 'Add To Lineup' : 'Out!';`

We create dynamic text on our button to either show `Add To Lineup` if player if `isAvailable` or `Out!` if they are not

## Use React tab
Change status of first player and watch the button update dynamically

It has a **CSS3 transition** applied to it so it will animate

`_button.scss`

```
button,input[type=submit] {
  text-transform: uppercase;
  background: none;
  border: 1px solid #000;
  font-weight: 600;
  font-size: 1.5rem;
  font-family: 'Open Sans';
  transition: all 0.2s;
  position: relative;
  z-index: 2;
}

button[disabled],input[type=submit][disabled] {
  color: #d12028;
  background: #fff;
  border-color: #d12028;
  transform: rotate(-10deg) scale(2) translateX(50%) translateY(-50%);
}
```

### Disable button if not available
If player isn't active we need to disable their button

`Player.js`

Update button with: 

`<button disabled={!isAvailable}>{buttonText}</button>`

* This is the **HTML5** disabled property that we set to `true` if player status is not `active`

## Where do we create a method to add to the Lineup Component?
`App.js`

```
import React from 'react';
import Header from './Header';
import Lineup from './Lineup';
import Roster from './Roster';
import samplePlayers from '../sample-players';
import Player from './Player';

class App extends React.Component {

  constructor() {
    super();
    this.addPlayer = this.addPlayer.bind(this);
    this.loadSamples = this.loadSamples.bind(this);
    this.addToLineup = this.addToLineup.bind(this);
    // initial state (was known as 'getinitialstate' with React createClass)
    this.state = {
      players: {},
      lineup: {}
    };
  }

  addPlayer(player) {
    // update our state
    const players = {...this.state.players};
    // add in our new player
    const timestamp = Date.now();
    players[`player-${timestamp}`] = player;
    // this.state.players.player1 = player;
    // set state
    this.setState({ players });
  }

  loadSamples() {
    this.setState({
      players: samplePlayers
    });
  }

  addToLineup(key) {
    // take a copy of our state
    const lineup = {...this.state.lineup};
    // update or add the new number of players added to lineup
    lineup[key] = lineup[key] + 1 || 1;
    // update our state
    this.setState({ lineup });
  }

  render() {
    return (
      <div className="team-of-the-day">
        <div className="menu">
          <Header tagline="Great Players" />
          <ul className="list-of-players">
            {
              Object
              .keys(this.state.players)
              .map(key =>
                <Player
                  key={key}
                  details={this.state.players[key]} addToLineup={this.addToLineup}
                />)
            }
          </ul>
        </div>
        <Lineup />
        <Roster
          addPlayer={this.addPlayer}
          loadSamples={this.loadSamples}
        />
      </div>
    )
  }
}

export default App;
```

## View in browser
Load sample players
**React** tab > `look for Player` > And you will see that `addToLineup()` method is bound to it

## Update `Player.js`

```
return (
        <li className="menu-players">
          <img src={details.image} alt={details.firstName} />
          <h3 className="player-name">
            <span>{details.firstName}</span> <span>{details.lastName}</span>
            <span className="price">{formatPrice(details.fee)}</span>
          </h3>
          <p>{details.comments}</p>
          <button disabled={!isAvailable} onClick={this.props.addToLineup}>{buttonText}</button>
        </li>
    )
```

We add a click event to our button `<button disabled={!isAvailable} onClick={this.props.addToLineup}>{buttonText}</button>`

### How do you pass an argument to `addToLineup`?
We don't want the `addToLineup` to fire on page load but when they click the button

`<button disabled={!isAvailable} onClick={() =>this.props.addToLineup('player01')}>{buttonText}</button>`

Now if you view in browser > `React` tab > Search for App > Look at lineup state > click `Add to Lineup` and you will see it increases every time

## Passing a dynamic argument
We don't want to hardcode `player-1`, `player-2`...

### Can we access the Player instance key inside a Component? 
No. Not right now

* If you need to access the `key` attribute, you need to explicitly pass it down
* **React** make it hard because the `key` is not for us, but if we do need it, we have to pass it down ourselves
* To get around this we create another attribute called `index` that will pass the same `key` value. Something like this:

```
<ul className="list-of-players">
            {
              Object
              .keys(this.state.players)
              .map(key =>
                <Player
                  key={key} index={key}
                  details={this.state.players[key]} addToLineup={this.addToLineup}
                />)
            }
          </ul>
```

Notice how we added the `key` value with both `key` and `index` attributes

View `Player` in React tab and you will now see index

### Update button with index
`Player.js`

```
<button disabled={!isAvailable} onClick={() => this.props.addToLineup(this.props.index)}>{buttonText}</button>
```

Now view in `browser` > `Load sample players` > `Click` add to lineup > Search for App and you will see when you expand `lineup` that you added that specific player you added

![player index added](https://i.imgur.com/HEdPhfi.png)

## Refactor our code with ES6

**note** 

```
// this ES6 destructuring 
const { details, index } = this.props;
// is the same as this
const details = this.props.detail;
const index = this.props.index;
```

Let's refactor with ES6 as it saves us typing

```
render() {
    const { details, index } = this.props;
    const isAvailable = details.status === 'active';
    const buttonText = isAvailable ? 'Add To Lineup' : 'Out!';
    return (
        <li className="menu-players">
          <img src={details.image} alt={details.firstName} />
          <h3 className="player-name">
            <span>{details.firstName}</span> <span>{details.lastName}</span>
            <span className="price">{formatPrice(details.fee)}</span>
          </h3>
          <p>{details.comments}</p>
          <button disabled={!isAvailable} onClick={() => this.props.addToLineup(index)}>{buttonText}</button>
        </li>
    )
}
```

