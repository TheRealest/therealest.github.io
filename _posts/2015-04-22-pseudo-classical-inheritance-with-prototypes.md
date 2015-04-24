---
title: Pseudo-classical inheritance with prototypes
date: 2015-04-22
---
<p class="intro"><span class="dropcap">P</span>rototypal inheritance is a major sticking point for many when learning Javascript. Compared to a classical inheritance system like most languages have, prototypes are unintuitive and can be unwieldly. Trying to use a constructor function as the prototype for another constructor in an attempt to imitate a subclassing pattern can often leave a new developer exasperated, copying and pasting code from StackOverflow just to make it work.</p>

### Simple object literal prototypes

It's not difficult to see how property lookups work with the prototype chain when you're using a simple object as the prototype for a constructor – something like this:

```js
var Dog = function(name, breed) {
  this.name = name;
  this.breed = breed;
};
Dog.prototype = {
  bark: function() {
    console.log('Ruff!');
  };
};
```

But if you had two constructors and you try to assign one to be the prototype of the other directly, you won't inherit properties as expected and it may not be obvious why.

### Passing down properties from one constructor to another

Let's say we have a constructor like the following:

```js
var Ticket = function() {
  this.number = Math.floor(Math.random()*100);
  this.winning = false;
  
  this.checkTicket = function(winningNumber) {
    this.winning = this.number === winningNumber;
    return this.winning;
  };
};
```

You would instantiate new `Ticket` objects with

```js
var myTicket = new Ticket();
```

Now `myTicket` has the public properties `myTicket.number`, `myTicket.winning`, and `myTicket.checkTicket`.

What if we want to create a `MagicTicket` class? Magic tickets are still tickets and have numbers and a winning status, but they also have a little extra magic to them. Let's define a `MagicTicket` class with this new functionality.

```js
var MagicTicket = function() {
  this.number = Math.floor(Math.random()*100);
  this.winning = false;
  
  this.checkTicket = function(winningNumber) {
    this.winning = this.number === winningNumber;
    return this.winning;
  };
  this.doMagic = function() {
    this.winning = true;
  };
};

var myMagicTicket = new MagicTicket();
```

This is one way of doing it, but if we want to add any new methods to `Ticket`, like a `ripUp` method, we would have to also copy the code into `MagicTicket`. Instead we can let `Ticket` be the prototype for `MagicTicket` so the shared methods are inherited.

Setting the prototype of a constructor function to inherit public methods is one of the most confusing thing about Javascript's prototypal inheritance pattern. You might think you could do this:

```js
var MagicTicket = function() {
  this.doMagic = function() {
    this.winning = true;
  };
};
MagicTicket.prototype = Ticket;

var myMagicTicket = new MagicTicket();
```

Let's look at what's going on when you call `var myMagicTicket = new MagicTicket()`. The returned object has a property `doMagic` as defined in the constructor function. But what happens if you try to access `myMagicTicket.checkTicket`?

`myMagicTicket` doesn't have a `checkTicket` property itself because one was never assigned in the `MagicTicket` constructor (you would see an assignment to `this.checkTicket` somewhere). Because of prototypal inheritance, Javascript will then look at the prototype object for the `checkTicket` property.

Now you might think `myMagicTicket.checkTicket` will refer to the function we defined inside the `Ticket` constructor. But the prototype of `myMagicTicket` is actually the `Ticket` function itself, and there is no `Ticket.checkTicket` property – only instances of `Ticket` have a `checkTicket` method. This means that the way we have things set up right now, `myMagicTicket.checkTicket` is undefined!

So how can we get this inheritance chain working the way we expect? Remember that as a constructor function, `Ticket` creates instances with the `new` keyword, and the *instances* will have a `checkTicket` method. We want to replace the prototype assignment in the previous block of code with:

```js
MagicTicket.prototype = new Ticket();
```

Now when we create an instance of `MagicTicket`, its protoype will be a `Ticket` instance, which has all the methods defined inside the `Ticket` definition. When we try to access `myMagicTicket.checkTicket` it will look up the prototype chain, find the `Ticket` instance, and return the method defined there.

If this is still confusing, think of it this way. The constructor `Ticket` is like a blueprint for instances – when you call it with `new` it creates a new object called `this`, mutates `this` according to its function body, and then returns `this`. In the case of `Ticket` it adds a property and a method. `Ticket` is a sort of factory for creating instances like `myTicket`.

`MagicTicket` is similarly a factory for creating instances like `myMagicTicket`. But we want `myMagicTicket` to behave like `myTicket` for the most part, just with the added properties described in the `MagicTicket` definition. Another way of putting it is that we want a `Ticket` instance like `myTicket` to act as a *prototype* for anything created by the `MagicTicket` factory.

<blockquote>Now it becomes clear why we needed to use <code>new</code> when setting the prototype for <code>MagicTicket</code>. How can a prototypical magic ticket be a ticket factory? But it does make sense for an actual ticket to serve as the prototype for a magic ticket.</blockquote>

This is what our code looks like now:

```js
var Ticket = function() {
  this.number = Math.floor(Math.random()*100);
  this.winning = false;
  
  this.checkTicket = function(winningNumber) {
    this.winning = this.number === winningNumber;
    return this.winning;
  };
};
var MagicTicket = function() {
  this.doMagic = function() {
    this.winning = true;
  };
};
MagicTicket.prototype = new Ticket();

var myTicket = new Ticket();
var myMagicTicket = new MagicTicket();
```

And the inheritence chain looks something like this:

```js
                                   Ticket
                                    |
                                    |  instantiates
                                    |
                  .prototype        v
  MagicTicket  --------------->  myTicket
     |                             .number
     |  instantiates               .winning
     |                             .checkTicket()
     v
myMagicTicket
  .doMagic()
```

### One last thing
We now know why we need to use an instance as a prototype rather than a constructor function to set up method inheritance, but we've glossed over something: the non-method properties defined in `Ticket`. Consider `myMagicTicket.number` – since `myMagicTicket` doesn't have a `number` property itself, this will return the `number` property defined on that `Ticket` instance we set to `MagicTicket.prototype`.

But that same instance of `Ticket` is used as the prototype for *all* `MagicTicket` instances, so the `number` property of every `MagicTicket` instance refers to the same number object.

```js
var myMagicTicket1 = new MagicTicket();
var myMagicTicket2 = new MagicTicket();
console.log(myMagicTicket1.number === myMagicTicket2.number);
// true every time
```

What we really want to do is have `MagicTicket` set the `number` property whenever a new instance is created, rather than relying on delegating to the shared property on the prototype, but we don't want to have to repeat that code. Another way of putting this is that when `MagicTicket` runs, it would be nice if we could get `Ticket` to also run using the same `this`.

Fortunately we can. Consider the following:

```js
var MagicTicket = function() {
  Ticket.call(this);
  this.doMagic = function() {
    this.winning = true;
  };
};
```

The line we added says "call the `Ticket` function and use the `this` object from `MagicTicket` as the `this` context inside `Ticket`". This will mutate the `this` object, adding properties as described in the `Ticket` function definition. Then the same `this` object has properties added to it by the rest of the `MagicTicket` function definition. This is like the `super` keyword available in many languages with a strictly classical inheritance system.

This solves our issue with every `MagicTicket` instance sharing the same number, but now it kind of seems like we wasted all that time messing with the prototype because it also assigns the `checkTicket` function to the `MagicTicket` instance directly. The inheritance chain looks like this now:

```js
                                   Ticket
                                    |
                                    |  instantiates
                                    |
                  .prototype        v
  MagicTicket  --------------->  myTicket
     |                             .number
     |  instantiates               .winning
     |                             .checkTicket()
     v
myMagicTicket
  .number
  .winning
  .checkTicket()
  .doMagic()

```

When you call any of those properties on `myMagicTicket` it won't even use the prototype chain because they're all defined on the object itself. In fact, we could remove the line that assigns a `Ticket` instance to `MagicTicket.prototype` and it wouldn't make any difference in this situation.

But there *are* times when it matters and you would still want to set the `prototype` property. Imagine `Ticket` had a prototype itself, like an instance of `Paper`.

```js
var Paper = function() {
  this.wrap = function() {
    console.log('In west Philadelphia, born and raised...');
  };
};
```

We now know that to make `Ticket` inherit from `Paper` we want to do something like:

```js
Ticket.prototype = new Paper();
```

So we can call `myTicket.wrap()` and the method on the prototype will be called. But can we do `myMagicTicket.wrap()` if we leave out the `MagicTicket.prototype` assignment? Let's look at the inheritance chain.

```js
                                                Paper
                                                 |
                                                 | instantiates
                                 .prototype      |
                         Ticket ------------> myPaper
                          |                     .wrap()
                          |  instantiates
                          |
                          v
  MagicTicket           myTicket
     |                    .number
     |  instantiates      .winning
     |                    .checkTicket()
     v
myMagicTicket
  .number
  .winning
  .checkTicket()
  .doMagic()

```

Now that `MagicTicket` is disconnected from the prototype chain, it doesn't have access to `wrap`!

<blockquote>Even though we used the <code>Ticket</code> constructor to add properties to <code>MagicTicket</code> instances, we still need to set <code>MagicTicket.prototype</code> if we expect to inherit properties or methods from the prototype of <code>Ticket</code>.</blockquote>

Prototypal inheritance is far from intuitive, but I think this demonstrates why you should be using instances instead of constructors as prototypes, why you might want to call one constructor from within another, and why that doesn't preclude you from using the prototype as well.

Here's the final code and prototype chain:

```js
var Paper = function() {
  this.wrap = function() {
    console.log('In west Philadelphia, born and raised...');
  };
};

var Ticket = function() {
  this.number = Math.floor(Math.random()*100);
  this.winning = false;
  
  this.checkTicket = function(winningNumber) {
    this.winning = this.number === winningNumber;
    return this.winning;
  };
};
Ticket.prototype = new Paper();

var MagicTicket = function() {
  Ticket.call(this);
  this.doMagic = function() {
    this.winning = true;
  };
};
MagicTicket.prototype = new Ticket();

var myPaper = new Paper();
var myTicket = new Ticket();
var myMagicTicket = new MagicTicket();
```

```js
                                                     Paper
                                                      |
                                                      |
                                                      | instantiates
                                      .prototype      v
                              Ticket ------------> myPaper
                               |                     .wrap()
                               |  instantiates
                               |
               .prototype      v
  MagicTicket ------------> myTicket
     |                        .number
     |  instantiates          .winning
     |                        .checkTicket()
     v
myMagicTicket
  .number
  .winning
  .checkTicket()
  .doMagic()

```

