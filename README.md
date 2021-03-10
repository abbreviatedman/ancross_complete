# 'AnCross', or 'a React Lesson about complex APIs'

### What are we doing?

**Glad you asked.** Today we will be making a react app that queries an API that returns an _object of objects_. We want to process that object and return an instance of each one, then make a feature where we can "favorite" as many instances as we want!

## Getting Started

To start, let's go ahead and instantiate a new react app. We'll use:

```bash
npx create-react-app villager_faves
```

and we won't need any additional NPM packages, so we're now ready to

```bash
cd villager_faves
code .
npm start
```

## Great! Now let's write--no, wait, DELETE some code.

The first thing I like to do once I know my React App is working is to delete the boilerplate code given to us by `create-react-app`. Delete the entire function... we're going to need a `class`!

All you should have left is the imports above and the `export default App` below.

And now delete the imports too!

Great! Now we have a basically empty workspace.

Now import `Component` from React, and define an App class with a `render` method. For now, just put a header in there so we know this is working when we run `npm start`. You should have something like the following:

```javascript
import { Component } from "react";

class App extends Component {
  render() {
    return <h1>Hello World!</h1>;
  }
}

export default App;
```

Run `npm start` and make sure you've got a heading rendered!

(If you don't, double-check that you're running it in your project's directory!)

## Let's hit up that API!

Import `axios` at the top of your file, then, in your class, add a `componentDidMount` method and let's make a request to an Animal Crossing API.

```js
componentDidMount() {
  fetc("http://acnhapi.com/v1/villagers/")
    .then((response) => response.json())
    .then((data) => {
      console.log(data);
    });
}
```

Super! Let's take a close look at what this is doing in our devTools console.

Rad! We should see a big object of objects. Now we're getting somewhere.

Now, how do we surf through an object and turn it into a useable iterable? We want to render a card for _each and every object in here_ so we know that this needs to be treated like an array. How do we do that?

Javascript has a really handy method called `Object.values()`. This method takes an _object_ as an argument, and returns all the _values_ within that object as an array. Let's test it out!

Adapt your `componentDidMount` to look like so:

```js
componentDidMount() {
  fetch("http://acnhapi.com/v1/villagers/")
    .then((response) => response.json())
    .then((data) => {
      const iGuessThisIsAnArrayOfObjectsNowHuh = Object.values(data);
      console.log(iGuessThisIsAnArrayOfObjectsNowHuh);
    });
}
```

Super! Now our crazily-named variable should represent an _array of objects_. This is much more usable given our app!

(You should probably change the name of that variable, though!)

(Just kidding. Never change, `iGuessThisIsAnArrayOfObjectsNowHuh`. _Never change._)

## Check for understanding

We've used React a little at this point, and we know we're going to have to use `this.state` in order to populate the state variable and render any data.

Set up a state object with a `names` property to take the array we're generating off our API call, then, in your `render`, iterate over that variable to render a list of every villager's name. Think about how we need to navigate this object to get the data we want to see!

You'll have to take a deep dive into each object, and you may have to do some research on JavaScript Object Bracket Notation if you're unfamiliar.

See if you can do it before you look at what's below!

<details>
<summary></summary>

Make an initial state.

```js
state = {
  villagers: [],
};
```

Set it our array of objects when we hit our API.

```js
componentDidMount() {
  fetch("http://acnhapi.com/v1/villagers/")
    .then((response) => response.json())
    .then((data) => {
      this.setState({ villagers: Object.values(data) });
    });
}
```

Render each villagers' name (on the name object, using bracket notation to make sure the dash isn't interpreted as a subtraction symbol.)

```js
return (
  <div>
    {this.state.villagers.map((villager) => (
      <p>{villager.name["name-USen"]}</p>
    ))}
  </div>
);
```

</details>

## Cool Beans.

We've made it to this point, and now the hard work is done. Let's polish this up a little bit.

I want to generate a brand new component to handle the render of all the results. Let's go ahead and make a component called `displayCards.jsx` inside of our `src` folder.

Within `displayCards.jsx` I just want to render all of my array elements, as opposed to within the `list` variable we made a bit ago. Let's refactor.

## Check for understanding #2:

When we refactor our code to render the names inside of `displayCards.jsx`, we could also take the time to render an image of each villager. How could we use what we learned with accessing our villager's _name_ property to render an image?
Work independently to see if you can get an image rendered for each villager.

<details>
<summary>Solution</summary>

Inside of `displayCards.jsx`

```js
<div>
  {props.data.map((villager, i) => {
    return (
      <div key={i} className="villagerCard">
        <img
          className="villagerPic"
          src={villager["image_uri"]}
          alt={villager.name["name-USen"]}
        />
        <p>{villager.name["name-USen"]}</p>
      </div>
    );
  })}
</div>
```

</details>
<br>
Got it? Well done!
At this point, we can relax and sit back, knowing that we can render API data on a page effectively.

## Take it further

But what if we wanted to add a feature where users could search for a particular villager? How could we implement dynamic search functionality, the like of which we used in Fruit Filter, here in this application? Take a moment to work independently and see if you can't develop a feature which can handle this!

<details>
<summary></summary>

```js
const dynamicSearch = () => {
  return data.hits.filter((villager) =>
    villager.name["name-USen"].toLowerCase().includes(search.toLowerCase())
  );
};
```

and within the return...

```js
<div className="searchBox">
  <input type="text" value={search} onChange={handleChange} />
</div>
```

</details>

Finish that quick? Pack it into a separate component for extra practice!!

## Even further! Favorite functionality:

Awesome!! Now our users can search for a particular villager, and _just_ render that villager. Neat!
But what if we wanted to develop a list of favorites? It's not that difficult, remember we have all the pieces we need! We just need to develop a new state variable to track villagers that the user likes.

Let's do just that:

```js
let [favs, setFavs] = useState([]);
```

Now, we just need to build a function that can populate that state. Let's stop and consider how we want our users to interact with our page.

For simplicity's sake, I think I would prefer if the user could simply click on a villager portrait, and that would populate that villager into the array. That's easy in react! Remember `onClick`?

```js
const handleClick = (villager) => {
  setFavs([...favs, villager]);
};
```

PAUSE! How is our spread operator working here? Why are we using it here?
React behaves a bit differently than we're used to. Traditionally, if we wanted to add to an array we would simply `.push()` to it, but React state, as we know, is a bit different.

We have a few different options to push array data to state, but this is a clean, easy to read and understand version. What we are doing is "spreading" the current favs state, and applying it to the "first" value of an array we're generating here. Hence the array notation. The _last_ thing in the array will be the _new villager_ we have created, the one being passed into `handleClick`.

Super!! So in terms of raw functionality, we now just need to display these villagers for the user to see. How can we reuse an existing React component to accomplish this?

**hint: we already have a displayCards component!**

<details>
<summary></summary>
In app.js...

```js
<div className="favBox">
  <DisplayCards data={favs} />
</div>
```

</details>

Ease McPease.

## But wait. There's more!

Our functionality is glorious at this point, but we currently have a few edge cases that need to be handled. Independently, try to work out what edge cases we need to handle, and how we can resolve them!

<details>
<summary>Don't you open this until you've given this a good shot.</summary>
First issue: We can send a villager to our faves array multiple times. While it does resemble my preference of having 45 instances of Chevre as my "favorite villagers" it's actually a fairly poor user experience. Here's how I might fix it:

```js
const handleClick = (villager) => {
  if (!favs.includes(villager)) {
    setFavs([...favs, villager]);
  }
};
```

simple enough!

Second issue: if we click a villager inside of our "faves" display, it results in an error. This is because we do _not_ want onClick functionality for our faves to share the onClick functionality with our standard display. There are great ways to fix this, but here's a lazy solution that will easily patch right over:

```js
//in app.js
 <div>
    <DisplayCards clickie={true} handleClick={handleClick} data={dynamicSearch()}/>
  </div>
  <div className="favBox">
    <DisplayCards clickie={false} data={favs} />
  </div>
```

Simple enough, I've added a boolean value as props to each of my displayCards components. Now let's handle the logic on the DisplayCards end.

```js
// in DisplayCards.js
<div
  key={i}
  className="villagerCard"
  onClick={() => (props.clickie ? props.handleClick(villager) : null)}
>
  <img
    className="villagerPic"
    src={villager["image_uri"]}
    alt={villager.name["name-USen"]}
  />
  <p>{villager.name["name-USen"]}</p>
</div>
```

Again, there are other solutions to this problem! But in this case, we've patched right over the error. If "clickie" is true, great! Run the handleClick. However, if it is _not_ then the computer will not even make the attempt to run the function. The onClick behavior resolves to `null`.

</details>

## Keep exploring!!

Explore more on this topic as you please; there's a lot of data within the animal crossing API, why not build out new app functionality? Have fun!
