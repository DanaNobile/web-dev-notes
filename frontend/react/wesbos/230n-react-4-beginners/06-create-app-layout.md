# Create application layout with components

## `src/components/App.js`

Save `TeamPicker.js` as `App.js`

```
import React from 'react';

class App extends React.Component {
  render() {
    return (
      <div className="team-of-the-day">
        <div className="menu">
          <Header />
        </div>
        <Lineup />
        <Roster />
      </div>
    )
  }
}

export default App;
```

View in browser. Nothing changes because `index.js` is still rendering `<TeamPicker />`

## Update `index.js`

```
import React from 'react';
import { render } from 'react-dom';
import './css/style.css';
import App from './components/App';

import TeamPicker from './components/TeamPicker';

render(<App/>, document.querySelector('#main'));
```

## Why do we get an error?
We get an error `Header is not defined` because we did not define the Header Component yet

## Create `/src/components/Header.js`
Save `App.js` and `Header.js`

```
import React from 'react';

class Header extends React.Component {
  render() {
    return (
      <p>Header</p>
    )
  }
}

export default Header;
```

## Update `App.js`

```
import React from 'react';
import Header from './Header';

class App extends React.Component {
  render() {
    return (
      <div className="team-of-the-day">
        <div className="menu">
          <Header />
        </div>
      </div>
    )
  }
}

export default App;
```

## View in browser and you'll see our `Header`

## Add Lineup and Roster Components
### Steps
1. Create the Components inside the `components` folder by saving `App.js` as each Component (`Lineup.js` and `Roster.js`) and rename the class name and export default name for both.
2. Add the module imports for each Component to top of the `App.js` file

`src/components/Lineup.js`

```
import React from 'react';

class Lineup extends React.Component {
  render() {
    return (
      <p>Lineup</p>
    )
  }
}

export default Lineup;
```

`src/components/Roster.js`

```
import React from 'react';

class Roster extends React.Component {
  render() {
    return (
      <p>Roster</p>
    )
  }
}

export default Roster;
```

## Update `src/components/App.js`

```
import React from 'react';
import Header from './Header';
import Lineup from './Lineup';
import Roster from './Roster';

class App extends React.Component {
  render() {
    return (
      <div className="team-of-the-day">
        <div className="menu">
          <Header />
        </div>
        <Lineup />
        <Roster />
      </div>
    )
  }
}

export default App;
```

### View in browser and you'll see Header, Lineup and Roster

#### Use React in Dev Tools to see the App, Header, Lineup and Roster Components

### Warning Will Rogers
You will see this annoying light yellow colored eslint TeamPicker warning in the console. Eslint is telling you you have a Component imported and you are not using it. This is a `no-no` in React. You only import what you will use. We will use this Component soon. I promise.


