# Searching for Videos
* We will pass a callback to SearchBar
* callback will take a string and make a new YTSearch and when search is complete it will set the `state` of the new list of videos

`index.js`

```
videoSearch(term) {
    
  }
```

* We will cut and paste YTSearch() into `videoSearch()`
    - No use in having duplicate code
    - We will rename the default search term from the static hardcoded `surf` to `term`

```
videoSearch(term) {
    YTSearch({key: API_KEY, term: term}, (videos) => {
      this.setState({
        videos: videos,
        selectedVideo: videos[0]
      });
    });
  }
```

But inside our constructor() we still want to do the initial search so we add:

`index.js`

```
import React, { Component } from 'react';
import ReactDOM from 'react-dom';
import YTSearch from 'youtube-api-search';
import SearchBar from './components/SearchBar';
import VideoList from './components/VideoList';
import VideoDetail from './components/VideoDetail';

const API_KEY = 'AIzaSyC9bJsdEJpmtGcBb_c7ck0NvOvyShMx47I';

class App extends Component {
  constructor(props) {
    super(props);

    this.state = {
      videos: [],
      selectedVideo: null
    };

    this.videoSearch('Pele');
  }

  videoSearch(term) {
    YTSearch({key: API_KEY, term: term}, (videos) => {
      this.setState({
        videos: videos,
        selectedVideo: videos[0]
      });
    });
  }

  render() {

    return (
      <div>
        <SearchBar />
        <VideoDetail video={this.state.selectedVideo} />
        <VideoList
          onVideoSelect={selectedVideo => this.setState({selectedVideo}) }
          videos={this.state.videos} />
      </div>
    );
  }
}

ReactDOM.render(<App />, document.querySelector('.container'));
```

### Pass new method into SearchBar Component
We can pass this method down into `SearchBar`

`<SearchBar onSearchTermChange={term => this.videoSearch(term)} />`

* When `SearchBar` calls `onSearchTermChange()` it will do so with a search term `term` and that will be sent right into `this.videoSearch(term)` and that function will run and do the YouTube video search

`SearchBar`

```
// more code
render() {
    return (
      <div className="search-bar">
        <input
          value={this.state.term}
          onChange={event => this.setState({ term: event.target.value})} />
      </div>
    )
  }
// more code
```

## Adding a separate method
Our current handler for the input change is tucked away on `state` (on the onChange) it is its own little function. If we add a callback in here our code will start to look ugly so we will split our event handler out into a separate method

`SearchBar`

```
import React, { Component } from 'react';

class SearchBar extends Component {
  constructor(props) {
    super(props);

    this.state = { term: 'We Begin' };
  }

  handleInputChange(term) {
    
  }

  render() {
    return (
      <div className="search-bar">
        <input
          value={this.state.term}
          onChange={event => this.handleInputChange(event.target.value)} />
      </div>
    );
  }
}

export default SearchBar;
```

* So now whenever the input is changed (`onChange`), we'll call `onInputChange()` with the new search term `event.target.value`
* Inside `handleInputChange()` we want to do two things:
    1. Set the `state` with the **term**
    2. We also want to call the **callback** that we got from `index.js` (from `App`) with the new search term

```
handleInputChange(term) {
    this.setState({term});
    this.props.onSearchTermChange(term);
}
```

## Test in browser
Save and refresh and type in search bar and the youtube videos update

## Video is lagging during search
Searching on every key typed is hurting performance, let's change it to only search every half second

## Review of what we did
1. We refactored the Youtube search into it's own method

`index.js`

```
videoSearch(term) {
    YTSearch({key: API_KEY, term: term}, (videos) => {
      this.setState({
        videos: videos,
        selectedVideo: videos[0]
      });
    });
  }
```

That method just takes a single string (which is a search term)

2. We took this method `videoSearch()` and we passed it down into `SearchBar` under the property `onSearchTermChange`

`<SearchBar onSearchTermChange={term => this.videoSearch(term)} />`

3. So all SearchBar has to do is call `props.onSearchTermChange()` with a new search term and that will call our searching function `videoSearch()` (inside `index.js` (App Component)) which will call the YouTube API and fetch a new list of videos

`SearchBar`

4. Inside of SearchBar we refactored the onChange event (whenever the content of the input changed it now calls handleInputChange(with the new input value (`event.target.value`)))

We have two purposes

```
handleInputChange(term) {
      this.setState({term});
      this.props.onSearchTermChange(term);
  }
```

* We set the state to the updated term
* It fires off the callback function onSearchTermChange

**note**

In `SearchBar` we have two callback functions and it might be too superfluous to have them both. We could have condensed them into one but this way we keep our one callback clean and just simply pass it one search term. One may argue our code is verbose but it is more readable. Beauty is in the eye of the beholder :) 
