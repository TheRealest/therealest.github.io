---
title: "eventful-react brings Backbone style events to React"
date: 2015-05-26
---

<p class="intro">One of React's defining characteristics is its strict one-way data flow. This is a blessing and a curse; while it adds a functional programming style to component rendering, limiting statefulness to the components where it is really necessary, it also makes it hard for deeply nested components that deal with UI event handlers to communicate with your components at the top of the hierarchy. <a href="https://github.com/TheRealest/eventful-react">eventful-react</a> solves this problem by exposing event emitter style methods on every component and allowing emitted events to bubble up the component hierarchy from deeply nested components all the way to the root.</p>

1. [The problem](#the-problem)
2. [An external event system](#an-external-event-system)
3. [The interface](#the-interface)
4. [Where this project is going](#where-this-project-is-going)

### The problem

The vanilla React way of solving this problem is explicitly passing a handler function from parent to child, and then letting the child call the parent's passed function when a UI event is triggered.

```js
var Parent = React.createClass({
  clickHandler: function() {
    console.log('catching the click in the parent!');
  },
  render: function() {
    return <div id="parent">
      <Child clickHandler={this.clickHandler} />
    </div>;
  }
});
var Child = React.createClass({
  render: function() {
    return <span className="child" onClick={this.props.clickHandler}></span>;
  }
});
```

This is fine when you're passing directly from parent to child, but you can imagine that this is a logistical nightmare if UI events are being triggered on components five or more layers deep. You have to pass the event handler functions down through every layer of components and ensure that your function makes it all the way down to the bottom of your tree. If you have a controller style component at the root, you need to manage passing handlers down through every component in your app, even for components that don't know or care about your events or handlers.

### An external event system

Facebook suggests you [use a global event system to avoid this behavior](https://facebook.github.io/react/tips/communicate-between-components.html):

> For communication between two components that don't have a parent-child relationship, you can set up your own global event system... Flux pattern is one of the possible ways to arrange this.

Flux is great when you have events that can come from a lot of different place and need to notify components in a lot of different places, but it also involves a lot of setting up. You need to create actions, manage a dispatcher, register stores, and all of this requires a degree of boilerplate just to notify your root component that a button somewhere down the tree was clicked. A lot of times Flux is overkill, but even when it's not, managing an external event system is a pain.

`eventful-react` takes care of the event system management for you in exchange for a simple rule: events bubble up from child to parent until they hit the root component. You can't use `eventful` to let one button notify a view that it was clicked directly, you can only notify your ancestors and let some component who is an ancestor of both the button and the view take care of sending the information along. For those that have used Backbone, this is similar to the way model events bubble up to their collection.

This is really more in line with React's way of doing things than letting any component register a listener for an event which could be emitted by any other component. Instead of information flowing sideways across the component tree, you should be sending the event up the tree until you hit a component that holds the data affected by the event, then modify the data accordingly and let React rerender the necessary components as the new data trickles down the component tree.

### The interface

To start using `eventful-react` you just need to add a script tag to your markup and switch out your calls to `React.createClass` with `Eventful.createClass`. `Eventful` will add your typical event emitter style methods (currently `emit` and `on`) and wrap some lifecycle methods to take care of the event system management mentioned above, and then delegate to React to generate the actual component object.

```js
var Parent = Eventful.createClass({
  componentDidMount: function() {
    this.on('click', this.clickHandler);
  },
  clickHandler: function(name) {
    console.log('catching the click ' + name + ' in the parent!');
  },
  render: function() {
    return <div id="parent">
      <Child />
    </div>;
  }
});
var Child = Eventful.createClass({
  clickHandler: function() {
    this.emit('click','child');
  },
  render: function() {
    return <span className="child" onClick={this.clickHandler}></span>;
  }
});
```

This is something of a contrived example, but you can see how any intermediate components would be free from passing on event handlers from parent to child. `Eventful` takes care of that external event system Facebook suggests and exposes a simple, familiar interface for registering callbacks, triggering events, and sending arguments up the component hierarchy.

### Where this project is going

I made this project as part of a two day "sprint" at MakerSquare where I'm currently studying, so there are bound to be bugs. I wrote tests for about 60% of the code, but I'd like to finish those and have a test behind everything. There are several methods beyond `emit` and `on` that I think are expected of a typical event emitter system (like `off` and `once` at the least) that I'd like to add. I wrote a simple demo demonstrating the differences between `eventful-react` and vanilla React, but I'd like to also write one for Flux as well.

If you'd like to play around with the code you can find it [on my Github](https://github.com/TheRealest/eventful-react). Any comments are more than welcome, as this is my first project like this.

Happy emitting!
