# Villager Search - A Complex API Journey

### What are we doing?

**Glad you asked.** Today we will be making a react app that queries an API that returns an _object of objects_. We want to process that object and return an instance of each one, then make a feature where we can "favorite" as many instances as we want!

## Getting Started

To start, let's go ahead and instantiate a new react app, right into your fork of this repo. We'll use:

```bash
create-react-app villager-search.
```

and we won't need any additional NPM packages, so we're now ready to

```bash
cd villager-search
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

Add a `componentDidMount` method and let's make a request to an Animal Crossing API.

```js
componentDidMount() {
  fetch("http://acnhapi.com/v1/villagers/")
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
<summary>Solution</summary>

Make an initial state.

```js
state = {
  villagers: [],
};
```

Put our array of objects in there once when we hit our API.

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

I want to generate a brand new component to handle the render of all the results. Let's go ahead and make a component called `DisplayCards.jsx` inside of our `src` folder.

Within `DisplayCards.jsx` I just want to render all of my array elements, instead of doing all that JavaScript right there within our `render` method.

Let's refactor!

## Check for understanding #2:

When we refactor our code to render the names inside of `DisplayCards.jsx`, we could also take the time to render an image of each villager. How could we use what we learned with accessing our villager's _name_ property to render an image?

Work independently to see if you can get an image rendered for each villager.

<details>
<summary>Solution</summary>

Rendered in `DisplayCards.jsx`:

```js
<div>
  {this.props.villagers.map((villager, i) => (
    <div key={i}>
      <img alt={villager.name["name-USen"]} src={villager["image_uri"]} />
      <p>{villager.name["name-USen"]}</p>
    </div>
  ))}
</div>
```

</details>

Got it? Well done!

At this point, we can relax and sit back, knowing that we can render API data on a page effectively.

## Take it further

But what if we wanted to add a feature where users could search for a particular villager? How could we implement dynamic search functionality?

Let's start implementing it in our `App.jsx`. We'll need:

- An input box with an `onChange` handler.
- In that handler, putting our input box's current value onto state, where we can easily manage it.
- In a helper method, return a `.filter`-ed version of our villagers array, where we check whether each villager's name `.includes` our state's `search` value (the value in our input box). Then make that method's return value what gets passed to our DisplayCards element! (Alternately, you can forget the helper method and use the .`filter` right in your render for simplicity's sake. But I think you'll see why a helper method is good here!)

Take a moment to work independently and see if you can't develop a feature which can handle this!

<details>
<summary></summary>

```js
dynamicSearch = () => {
  return this.state.villagers.filter((villager) =>
    villager.name["name-USen"]
      .toLowerCase()
      .includes(this.state.search.toLowerCase())
  );
};
```

and within the return...

```js
<div>
  <input type="text" onChange={this.handleChange} />
  <DisplayCards villagers={this.dynamicSearch()} />
</div>
```

</details>

Whew! Pat yourself on the back and search for some adorable animal villagers for a while. You deserve it!
