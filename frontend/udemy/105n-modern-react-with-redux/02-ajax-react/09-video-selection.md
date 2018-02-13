# Video Selection
Refresh our app in the browser and you'll briefly see 'Loading' on the screen. This is happening because we are checking for the existence of a video before we render and show 'Loading' until we get a video

## Should we add a check to every Component?
No. Just when we really need it. You want to avoid your user loading a page and 10 spinners are popping up

**tip** It general, it is fairly safe to place an AJAX spinner on a high level/parent Component

## Selecting other videos
Give the user the ability to select another video and see that pop up on the screen

## Concept
Add the concept of a selected video to the `App` Component **state**

the `selectedVideo` will be a video object and it will be always passed to `VideoDetail` and instead of passing in `this.state.videos[0]` we can just pass in `selectedVideo`

## How will we update the selectedVideo?
We'll pass a callback from `App` into `VideoList` and then from `VideoList` into `VideoListItem`

Whenever the `VideoListItem` is clicked it will run the callback with the video that belongs to it

### Add selectedVideo to `state`
Change this:

```
class App extends Component {
  constructor(props) {
    super(props);

    this.state = { videos: [] };

    YTSearch({key: API_KEY, term: 'surfboards'}, (videos) => {
      this.setState({ videos });
    });
  }
```

to this

```
class App extends Component {
  constructor(props) {
    super(props);

    this.state = {
      videos: [],
      selectedVideo: null
    };
```

* We set `selectedVideo` initially to `null` because we don't have a selectedVideo to start with

## Next Step
Inside `index.js` and our `App` Component, pass the `selectedVideo` property of `state` into the `VideoDetail` instance

```
render() {

    return (
      <div>
        <SearchBar />
        <VideoDetail video={this.state.selectedVideo} />
        <VideoList videos={this.state.videos} />
      </div>
    );
  }
```

## View and test in browser
Save and refresh and you'll see we are stuck on `Loading...` and that makes sense because we are never changing the `selectedVideo` state value as it is always set to `null`, so in VideoDetail we always catch the check (if statement) which will always render `Loading...`

### Fixing `null` to be an initial video
```
YTSearch({key: API_KEY, term: 'surfboards'}, (videos) => {
      this.setState({ videos });
    });
```

We'll need to break this back out into the long form syntax instead of the shortened ES6 syntax

```
    YTSearch({key: API_KEY, term: 'surfboards'}, (videos) => {
      this.setState({ videos: videos });
    });
```

### Set default starting video
```
YTSearch({key: API_KEY, term: 'surfboards'}, (videos) => {
      this.setState({
        videos: videos,
        selectedVideo: videos[0] 
      });
    });
```

* App boots up, **videos** in state is an empty array and **selectedVideo** is `null`
* We go into VideoDetail Component, this Component is initially not provided a video so we show `Loading...`
* At the same time we kick off a request to grab a list of videos
    - When that request is completed and we get a response of videos, we'll pass the list of videos onto the array using this.setState({videos}) and the first video on that list will be set to `selectedVideo`
    - Since we are setting `state` here, it causes our Component to re-render, which means `VideoDetail` will be rendered again but this time the it will pass the check because we now have a `video`

### Test in browser
You should see we see our videos and we have

### Now we need to implement our callback!
This callback will be a function that we are going to pass from `App` to `VideoList` and finally into `VideoListItem`

* We are going to pass another function to `VideoList`

```
<VideoList
 onVideoSelect={selectedVideo => this.setState({selectedVideo}) }
 videos={this.state.videos} />
```

So we just defined a function and all it does is it takes a **video** (selectedVideo) and it defines it on `App`'s **state** and updates `App` **state** with a new video

So if `VideoList` calls this function with a video, the `selectedVideo` on `App` is going to update

This is a jump in logic. We are passing a function that manipulates another Component. This is confusing. Let's continue building and then review to help build clarity around the functionality we are building

**note** We are passing this as a property to VideoList so that means that VideoList now has a property on `props` called `props.onVideoSelect`

`VideoList`

```
import React from 'react';
import VideoListItem from './VideoListItem';

const VideoList = (props) => {
  const videoItems = props.videos.map((video) => {
    return <VideoListItem video={video} key={video.etag} />
  });

  return (
    <ul className="col-md-4 list-group">
     {videoItems}
    </ul>
  );
};

export default VideoList;
```

Update to multi-line statement:

```
const videoItems = props.videos.map((video) => {
    return (
      <VideoListItem video={video} key={video.etag} />
    );
  });
```

Add our new function (All we are doing here is we are taking the `prop` that was passing down from `App` and we are passing it down to `VideoListItem`)

```
const videoItems = props.videos.map((video) => {
    return (
      <VideoListItem
        onVideoSelect={props.onVideoSelect} 
        video={video}
        key={video.etag} />
    );
  });
```

Now we have access to `onVideoSelect` inside `VideoListItem`

Nice ES6 trick to clean up code

`VideoListItem`

Change this:

`const VideoListItem = ({video}) => {`

To this:

`const VideoListItem = ({video, onVideoSelect}) => {`

* This enables us to pull multiple properties off of the `props` object

**note** The above ES6 nicety in coding is identical to coding it this way:

```
const VideoListItem = (props) => {
  const video = props.video;
  const onVideoSelect = props.onVideoSelect
```

So we managed to transport our callback from `App` all the way down inside `VideoListItem`

## Let's make use of this callback
Whenever a user click's on the entire `LI` in `VideoListItem` (we'll use `onClick`), we'll pass it a function and call `onVideoSelect()` and we'll pass it the argument `video` (that video is this particular's VideoListItem's video)

```
return (
    <li className="list-group-item" onClick={() => onVideoSelect(video)}>
```

## View in browser
Save and refresh. Click any of the videos and you will see the main video update with the video you selected

### It is working
Let's review how it is working

We will start in `App`. Our top level Component. We defined a function in our VideoList that has one purpose, it just updates `App` **state**

`index.js`

```
<VideoList
          onVideoSelect={selectedVideo => this.setState({selectedVideo}) }
```

* All it does is it takes a video and it updates the selectedVideo
* We pass this as a property into `VideoList`

`VideoList`

* `VideoList` takes that property and it passes it into `VideoListItem`

```
<VideoListItem
        onVideoSelect={props.onVideoSelect} 
```

`VideoListItem`

`VideoListItem` takes that property `const VideoListItem = ({video, onVideoSelect}) => {` and says,

```
return (
    <li className="list-group-item" onClick={() => onVideoSelect(video)}>
```

"Whenever I get clicked call that function with the video I was passed" and since each `VideoListItem` has a different video, you will end up with the correct video

Passing callbacks from a parent Component to a child Component is kind of a rare thing to go more than two levels deep (it is not a rare thing but a rare thing to go more than two levels deep)

We went from `App` to `VideoList` to `VideoListItem`

### Passing callbacks
Is a great way to do small communication between a parent Component and a child Component

because we passed it down three Component's if we were working on a team and someone joined our team they would be inside `VideoListItem` and they would have to do a treasure hunt to figure out where the `onVideoSelect()` function came from



