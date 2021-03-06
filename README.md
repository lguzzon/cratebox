![Cratebox Logo](https://github.com/alexvcasillas/cratebox/blob/master/logo/cratebox-logo.jpg?raw=true)

[![CircleCI](https://circleci.com/gh/alexvcasillas/cratebox.svg?style=svg)](https://circleci.com/gh/alexvcasillas/cratebox) [![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com) [![first-timers-only](http://img.shields.io/badge/first--timers--only-friendly-blue.svg?style=flat-square)](http://www.firsttimersonly.com/)

**Cratebox** is an Opinionated State Management library for no particular UI library.

# Contents

- [Contents](#contents)
- [Motivation](#motivation)
- [Why Cratebox?](#why-cratebox)
- [Installation](#installation)
- [Getting Started](#getting-started)
- [Describing a Store](#describing-a-store)
- [Dispatching changes](#dispatching-changes)
- [Retrieving State](#retrieving-state)
- [Subscriptions](#subscriptions)
- [Time Traveling](#time-traveling)
- [Types](#types)
  - [Basic Types](#basic-types)
  - [Advanced Types](#advanced-types)
    - [types.array(type: base)](#typesarraytype-base)
    - [types.enum(enumeration: string[])](#typesenumenumeration-string)
    - [types.literal(literal: string)](#typesliteralliteral-string)
    - [types.frozen(frozen: object)](#typesfrozenfrozen-object)
- [Implementations](#implementations)
  - [React](#react)
- [Roadmap](#roadmap)
- [Thanks!](#thanks)

# Motivation

I started working on **Cratebox** to prove myself I was able to build a powerful state management library following the example of top notch developers like _Michel Weststrate_, _Dan Abramov_, _James Kyle_ and many more. I wanted something robust and typed like _MobX State Tree_ but also wanted something clear and easy to use, and that's why I built **Cratebox**, to be an intermediate point between _MobX State Tree_ and _Redux_, robust and with a declarative and simple API that does the job always.

# Why Cratebox?

This is a question that often comes to my mind. Why you should use **Cratebox**?. The answer could be: because you might like it. Or because you find an opinionated library interesting so you don't have to overwhelm yourself with decisions. Maybe it's because you like this simple API that it has. Using **Cratebox** is up to you. I've been working with _Redux_ and _MobX State Tree_ for a very long time and I believe **Cratebox** is right in between of this to impressive libraries and will get things done if you give it a try, for sure!

# Installation

NPM

```sh
npm install cratebox
```

Yarn

```sh
yarn add cratebox
```

# Getting Started

To get started with **Cratebox** first of all you need to import the dependency into your project plus the typings dependency for your models.

```js
import { cratebox, types } from "cratebox";
// Instantiate CrateBox
const myCratebox = cratebox();
```

From that point on, you'll have access to the full API of **Cratebox**.

# Describing a Store

```js
myCratebox.describeStore({
  identifier: "user",
  model: {
    name: types.string,
    lastName: types.string,
    avatar: types.string,
    age: types.number,
    birthDate: types.date,
    position: types.string,
    github: types.string,
  },
});
```

Let's take a look at the example above and let's get though all of the details.

First of all we have the `describeStore` function. This function is the one in charge to tell **Cratebox** that we would like to describe a new store were we will put our state.

The `describeStore` function takes on parameter that is an object will the following structure:

```js
{
  identifier: string,
  model: model object
}
```

The `identifier` property represents the name of the store you're describing, therefore, this will be the name you'll be using latter on to deal with this particular store within **Cratebox**.

The `model` property is a `model object` that represents all of the properties that you're identified store will contain. This properties could be any of the properties that we will describe later on. This properties are typed by our own typing system and will let you build rock-solid-typed models.

As we can see at the `describeStore` example above, we're defining a model that contains the following properties:

```
name: types.string,
lastName: types.string,
avatar: types.string,
age: types.number,
birthDate: types.date,
position: types.string,
github: types.string,
```

Many of the properties described above are of type `string`, meaning that no mater what you store there, if it's not a `string`, it will complain about it and, therefore, it wont let you store it at the given property.

There's also a type `number` and a type `date` there, meaning that, the property `age` will only be able to store numeric data and the `birthDate` property will only be able to store instances of the `Date` type.

Ok, we've described our store so far at this point. Let's proceed with more stuff about **Cratebox**.

# Dispatching changes

To dispatch a change into a store, we will call the `dispatch` function from the **Cratebox** API.

```js
myCratebox.dispatch({
  identifier: "user",
  model: {
    name: "Alex",
    lastName: "Casillas",
    age: 28,
    birthDate: new Date("1990-03-23"),
    avatar: "https://avatars3.githubusercontent.com/u/9496960?s=460&v=4",
    position: "Frontend Engineer",
    github: "https://github.com/alexvcasillas",
  },
});
```

Let's take a minute to process the code above. Got it? Ok, let's go for it.

The `dispatch` function takes on single argument that is a `dispatch object`. This `dispatch object` contains the following properties:

```js
{
  identifier: string,
  model: model object
}
```

See some resemblance from something we described before? That's it! It has the same structure that our `described store`! That makes things even more easy to deal with.

The `identifier` property will tell **Cratebox** which of the `described stores` is the one that's getting a change. In the example above, `user` store is the one that's getting a change dispatched.

We can dispatch as many changes to a store as we want. You have to consider that you dispatch changes to a model, meaning that, calling the `dispatch` event with the following data:

```js
myCratebox.dispatch({
  identifier: "user",
  model: {
    name: "Antonio",
    lastName: "Cobos",
    position: "Backend Engineer",
  },
});
```

Won't generate a new state with just:

```js
{
  name: 'Antonio',
  lastName: 'Cobos',
  position: 'Backend Engineer',
}
```

But instead will be:

```js
{
  name: 'Antonio',
  lastName: 'Cobos',
  avatar: 'https://avatars3.githubusercontent.com/u/9496960?s=460&v=4',
  age: 28,
  birthDate: '1990-03-23',
  position: 'Backend Engineer Engineer',
  github: 'https://github.com/alexvcasillas'
}
```

As you can see, it will generate changes based on the previous state, making sure that state changes to a single value won't break the state of your app.

You also have to take note that properties that _are not described_ at the `describeStore()` function, won't affect your state if you try to dispatch properties not defined previously. For example:

```js
myCratebox.dispatch({
  identifier: "user",
  model: {
    name: "Antonio",
    lastName: "Cobos",
    position: "Backend Engineer",
    mightyLevel: "Over 9000!!",
  },
});
```

This `dispatch` call will generate a new state with the described properties updated but the `mightyLevel` property will be discarded.

# Retrieving State

You can retrieve the current state of a specific store making use of our simple exposed API method for this: `getState(identifier: string)`

This method will retrieve the current state of a store by the given identifier. Let's look at an example:

```js
// Describe the store
myCratebox.describeStore({
  identifier: "user",
  model: {
    name: types.string,
    lastName: types.string,
  },
});

// Dispatch a new change at the user store
myCratebox.dispatch({
  identifier: "user",
  model: {
    name: "Alex",
    lastName: "Casillas",
  },
});

// Call the Get State method
console.log(myCratebox.getState("user"));
```

The code above will give you the following output in your console:

```js
{
  name: 'Alex',
  lastName: 'Casillas',
}
```

# Subscriptions

Subscriptions will let you listen to changes to a given store. This way you'll be able to update your UI (for example) based on state changes you made to your store.

The way of subscribing to changes within a particular store is:

```js
myCratebox.subscribe("user", model => {
  // Handle your changes the way you want here :)
});
```

Let's dive into the `subscribe` method now.

This method takes two arguments, the `identifier` of the store you want to track state changes to and a callback function that will give you the current `state model` after a `dispatch` action make changes to a store.

Let's see the following case as example:

```js
// Describe the store
myCratebox.describeStore({
  identifier: "user",
  model: {
    name: types.string,
    lastName: types.string,
  },
});

// Create a subscriber for the user store
myCratebox.subscribe("user", model => {
  console.log(model);
});

// Dispatch a new change at the user store
myCratebox.dispatch({
  identifier: "user",
  model: {
    name: "Antonio",
    lastName: "Cobos",
  },
});
```

At the right moment you `dispatch` the changes to the `user store`. The subscription hook will be called and you'll get the following output logged at the console:

```js
{
  name: 'Antonio',
  lastName: 'Cobos',
}
```

As you can see, subscriptions are pretty simple to use and you don't need to be subscribing to all of your stores' changes, you can subscribe to specific a store changes right whenever you need them.

# Time Traveling

Time traveling is supported out of the box for you. We expose a simple API that will let you handle time traveling in a simple way.

Let's say you want to travel backgrounds in your store, then you simply:

```js
myCratebox.travelBackwards("user");
```

Or let's say you want to travel forwards after you just traveled backwards, simply:

```js
myCratebox.travelForwards("user");
```

Let's dive a little into the Time Traveling API. It's simple, we expose to methods: `travelBackwards` and `travelForwards`.

Both of the methods require one single argument: the store `identifier` of the store you want to make time travel with.

Let's take a look at this with a little example:

```js
// Describe the store
myCratebox.describeStore({
  identifier: "user",
  model: {
    name: types.string,
    lastName: types.string,
  },
});

// Create a subscriber for the user store
myCratebox.subscribe("user", model => {
  console.log("Store Changes: ", model);
});

// Dispatch a new change at the user store
myCratebox.dispatch({
  identifier: "user",
  model: {
    name: "Alex",
    lastName: "Casillas",
  },
});

// Dispatch another change at the user store
myCratebox.dispatch({
  identifier: "user",
  model: {
    name: "Antonio",
    lastName: "Cobos",
  },
});

// Call the Travel Backwards method
myCratebox.travelBackwards("user");
// Call the Travel Forwards method
myCratebox.travelForwards("user");
```

When executing the example above you'll have the following output:

```js
// First Dispatch
Store Changes: { name: 'Alex', lastName: 'Casillas' }
// Second Dispatch
Store Changes: { name: 'Antonio', lastName: 'Cobos' }
// Travel Backwards
Store Changes: { name: 'Alex', lastName: 'Casillas' }
// Travel Forwards
Store Changes: { name: 'Antonio', lastName: 'Cobos' }
```

If you have noticed, the same `subscription` you've created for listening to changes when `dispatch` is called, will also work for Time Traveling out of the box.

# Types

**Cratebox** is a typed state management library and therefore, comes bundled with basic types plus some advanced types.

All of the types are to be imported from the `types` namespace.

## Basic Types

The basic types are the core types of any (almost every) programming language. We use this types to build a solid structure for our data. Those types are the following:

- **String Type** -  this type can contain any value of type string and null.
- **Number Type** -  this type can contain any value of type number and null.
- **Boolean Type** -  this type can contain any value of type boolean.
- **Null Type ** - this type can contain any value of type null.
- **Undefined Type** -  this type can contain any value of type undefined.
- **Date Type**  - this type can contain any value of type Date.

## Advanced Types

The advanced types of **Cratebox** are currently a work in progress but will contain some of the following types for your use:

### types.array(type: base)

The array type recieves a basic type as a single parameter and will make sure that all of the values stored at the array are solely that type.

For example:

```js
myCratebox.describeStore({
  identifier: "user",
  model: {
    name: types.string,
    lastName: types.string,
    notes: types.array(types.string),
  },
});
```

If you try to include any type different from the base string type it will complain and throw an error.

### types.enum(enumeration: string[])

The enumeration type recieves an array of literal strings as a single parameter and will make sure that the value that is trying to be store is one of the described in the enumeration.

For example

```js
myCratebox.describeStore({
  identifier: 'notes',
  model: {
    title: types.string,
    description: types.string
    status: types.enum(['DRAFT', 'PUBLISHED', 'HIDDEN'])
  }
});
```

If you try to include anything that is not one of the described string literals it will complain and throw an error.

### types.literal(literal: string)

The literal type recieves a string literal to check against.

For example

```js
myCratebox.describeStore({
  identifier: 'notes',
  model: {
    title: types.string,
    description: types.string
    status: types.enum(['DRAFT', 'PUBLISHED', 'HIDDEN']),
    fixed: types.literal('IMMUTABLE')
  }
});
```

If you try to set the property `fixed` to another value that's not IMMUTABLE, in this example, it will complain and throw an error.

### types.frozen(frozen: object)

The frozen type receives an object with a typed object structure.

```js
myCratebox.describeStore({
  identifier: 'notes',
  model: {
    title: types.string,
    description: types.string
    status: types.enum(['DRAFT', 'PUBLISHED', 'HIDDEN']),
    fixed: types.literal('IMMUTABLE'),
    auth: types.frozen({
      name: types.string,
      lastName: types.string,
      age: types.number,
      admin: types.boolean
    })
  }
});

myCratebox.dispatch({
  identifier: 'notes',
  model: {
    auth: {
      name: 'Alex',
      lastName: 'Casillas',
      age: 28,
      admin: true
    }
  }
})
```

Frozen type will check for it's inner typed properties everytime you make a dispatch to any of this properties to make sure that all of them meet the specified requirements.

# Implementations

Currently Cratebox has a React implementation.

## React

To make use of Cratebox with React, go to the official [Cratebox React](https://github.com/alexvcasillas/cratebox-react) bindings.

# Roadmap

We have a roadmap for new implementations and this are our intentions:

- Specific State Time Traveling via `travelTo(index: number)` exposed API method.
- Advanced Types
  - Optional Type
- Lifecycle hooks
  - Before Create via `beforeCreate` model property.
  - After Create via `afterCreate` model property.

# Thanks!

[Michel Weststrate](https://twitter.com/mweststrate)'s incredible work on [MobX](https://github.com/mobxjs/mobx) and [MobX-State-Tree](https://github.com/mobxjs/mobx-state-tree) that has been a great inspiration since their early stages.

[Dan Abramov](https://twitter.com/dan_abramov)'s work on [Redux](https://github.com/reactjs/redux) that also has been a great inspiration while trying to mix it with [MobX-State-Tree](https://github.com/mobxjs/mobx-state-tree)

And also lots of thanks to you for reading this lines and took some time to read though the docs and maybe gave a try to this project.
