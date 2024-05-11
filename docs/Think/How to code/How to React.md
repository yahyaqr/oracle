React is a free and open-source JavaScript library for building user interfaces or front-end webpage components such as forms along with fields like text areas and buttons.

Since its release in 2013, React has been maintained by Meta (formerly Facebook) along with a community of developers and companies.

It is component-based. An example of a component could be a form or even just a form field or button on a website. In React, applications are completely built with components.

Some of the most persistent issues of front-end development are solved with React, allowing one to build dynamic and interactive web apps with ease. It is fast, scalable, flexible, powerful, and has a robust developer community that’s rapidly growing.
## Components

In React, **components** are pieces of reusable, independent code that make up the user interface (UI) of the application. Each component comes with one or both of the following:

- A [[#state]] object that contains component data that is expected to change over time.
- A [[#props]] object with data that can be passed down from parent component to child component.

#### Class Components

Class components contain a state and use a `render()` function to return [[#JSX]] markup. When defined, the `class` has to be an extension of the `React.Component` class:

```jsx
import React from 'react';

class ClassComponent extends React.Component {
  this.state = {
    property1: "A string",
    property2: 1,
    property3: true
  }
  
  render() {
    return (
      <div>
        ...
      <div>
    )
  };
};
```


#### Function Components

Function components are stateless and only use the `return` statement. No import statement necessary:

```jsx
function FunctionComponent(props) {
  return (
    <div>
      ...
    <div>
  )
}
```

#### Arrow Function

The arrow function is a feature that was introduced in ES6. It uses the `=` and `>` characters to define a function like a variable. In React, function components can be defined with this syntax:

```jsx
const FunctionComponent = (props) => {
  return (
    <div>
      ...
    <div>
  )
}
```

## Context

The **Context** API in [[#React]] is an easy way to manage the state of some information. It lets the parent [[#Components]] pass the information down to any other component further down the tree hierarchy without needing to pass it as a [[#Props]] It can be used in combination with the [[#useState()]] hook to change the state. Typical use cases are passing themes (e.g. color, paddings, font sizes, etc.) or the authenticated user.

#### Benefit

Normally, information on values is passed between components as props. But sometimes it has to be passed down several levels in the tree, also called `prop drilling`. In larger applications, this can be complicated and lead to code that is hard to maintain. With `Context` this is no longer necessary.

#### API Implementation

To implement the Context API, it’s necessary to first create the Context using [[#createContext()]]. Afterward, the [[#useContext()]] hook can be used to read the context from the appropriate component.

#### createContext()

The **createContext()** function is used to create an instance of the [[#Context]] API that other components can read. It should be used in combination with the [[#useContext()]] hook.

**Syntax**

```jsx
import { createContext } from 'react';

const MyContext = createContext(defaultValue);
```

- The `defaultValue` can either be another context made from the `createContext()` method or a primitive data type, including `null`.
- `createContext()` returns a context object that should be named in `PascalCase`.

To pass the context down further, it is necessary to wrap the component tree in a context provider. A `value` prop needs to be passed inside the provider.

**Example**

```jsx
export default function App() {
  const theme = 'light';

  return (
    <ThemeContext.Provider value={theme}>
      <Container />
    </ThemeContext.Provider>
  );
}
```

Components have to use the [[#useContext()]] hook to get the context information.

> **Note**: In legacy code, there may not only be `Providers` but also `Consumers` that wrap the components. This is no longer recommended.

## Hooks

In React, **hooks** are functions that give class-like abilities to function [[#Components]], including [[#State]] and side-effects.

There are a few rules when using hooks, including the following:

- Hooks must be called from React functions (i.e. components or custom hooks) and not from a regular JavaScript function.
- Hooks should not be called inside of a loop, condition, or nested function. Instead, hooks should be called at the top level of a React function so they render in the same order every time.

While there are standard React hooks, like `useState()` and `useEffect()`, there are also custom-made hooks!

```jsx
import React, { useHook } from 'react';
```

Hooks are imported at the top of a file from the `react` library:

> **Note:** The code snippet above is purely pseudocode and useHook is not an actual React hook.

#### useContext()

The **`useContext()`** hook subscribes a child [[#Components]] to a [[#Context]] which includes its `value` [[#Props]] that exists further up the component tree.

**Syntax**

The components can read the `Context` value using the `useContext()` hook:

```jsx
const myValue = useContext(MyContext);
```

The `useContext()` hook searches for the closest provider above the component in which it was called within the tree hierarchy. Therefore, providers within the same component are ignored. Additionally, `useContext` needs to be imported from `react` at the top of the file.

Context is passed to other components by wrapping them in a context provider component. Inside the component, a `value` prop has to be created and assigned a value:

```jsx
<MyContext.Provider value={theme}>
  {/*
    Child components that subscribe to the context
    through the context provider.
  */}
</MyContext.Provider>
```

**Example 1**

The following example shows a single file, `App.js`, and how a background theme could be passed down to all child components via the `useContext()` hook. The `value` prop in this case is the `theme` state:

```jsx
// App.js
import { createContext, useContext, useState } from 'react';

const ThemeContext = createContext();

export default function App() {
  const [theme, setTheme] = useState('white');

  return (
    <ThemeContext.Provider value={theme}>
      <Button setTheme={setTheme} />
      <Container />
    </ThemeContext.Provider>
  );
}

function Button(props) {
  const theme = useContext(ThemeContext);
  const textColor = theme === 'white' ? 'black' : 'white';

  return (
    <button
      style={{
        background: theme,
        color: textColor,
        border: `1px solid ${textColor}`,
        borderRadius: 6,
      }}
      onClick={() => {
        theme === 'white' ? props.setTheme('black') : props.setTheme('white');
      }}
    >
      Click me to change the color
    </button>
  );
}

function Container() {
  const theme = useContext(ThemeContext);
  const textColor = theme === 'white' ? 'black' : 'white';

  return (
    <div
      style={{
        background: theme,
        color: textColor,
        border: `1px solid ${textColor}`,
        borderRadius: 6,
      }}
    >
      <h2>I will change my color!</h2>
      <h6>So will the background!</h6>
    </div>
  );
}
```

The `useContext()` hook was used to apply a `ThemeContext` to the `<Container>` component to switch the colors of the text and background between black and white.

The `theme` state is changed through the `setter` function that is passed to the `Button` component as a prop. The component uses it in the `onClick` event. In the example above, the theme is changed to black if the theme is white and vice versa.

**Example 2**

The following example shows how the `useContext()` hook can subscribe a component to a context imported from another file:

```jsx
// App.js
import { createContext } from 'react';
import { Display } from './Display';

export const NotificationContext = createContext();

export function App() {
  const notifications = [
    'This is awesome!',
    'And so versatile!',
    'I really like this',
  ];

  return (
    <NotificationContext.Provider value={notifications}>
      <Display />
    </NotificationContext.Provider>
  );
}
```

Now, other files can import the `NotificationContext`, along with the exported `Provider`, to be applied to their components:

```jsx
// Display.js
import { useContext } from 'react';
import { NotificationContext } from './context.js';

export function Display() {
  const notifications = useContext(NotificationContext);
  return (
    <div>
      {notifications.map((notification) => {
        <h2 key={notification}>{notification}</h2>;
      })}
    </div>
  );
}
```

#### useEffect()

The `useEffect()` hook takes in a function and an array. The function will be executed after the current render process finishes and only if the elements inside the array has changed from the previous render.

This hook can be used to run side effects (call to an external API, update another state, etc.) or attach event listeners.

**Syntax**

The `useEffect()` hook accepts a function and an array of dependencies as its first and second parameter respectively. This hook doesn’t return any value.

```jsx
useEffect(
  () => {
    // Runs side effect here
  },
  [] // Array of dependencies
);
```

If the array of dependencies is empty, the effect will only run once when the component mounts.

The function passed as the first parameter may also return a “cleanup function” that is executed before the next scheduled effect runs. This chance can be used to remove event listeners or abort an API call.

```jsx
useEffect(
  () => {
    // Runs side effect here
    
    return () => {
      // Do clean up here
    };
  },
  [] // Array of dependencies
);
```

**Example**

In the following example, the `useEffect()` hook attaches a `'scroll'` listener to the `window` object and removes it with a cleanup function:

```jsx
import React, { useEffect } from 'react';

function PageWrapper() {
  useEffect(() => {
    function scrollHandler() {
      console.log('Current scroll position is', window.scrollTop);
    }
    
    window.addEventListener('scroll', scrollHandler);
    
    return () => window.removeEventListener('scroll', scrollHandler);
  }, []); // Runs only once when component mounts

  return <div>Page content...</div>;
}
```

#### useRef()

The **`useRef()`** hook in React is used to create mutable references to elements or values within functional [[#Components]]. It enables direct access and interaction with DOM elements, allowing the persistence of data across renders without triggering re-renders.

**Syntax**

In the `useRef()` hook, the `initialValue` is an optional parameter that can be provided when creating a new ref object.

`initialValue` allows the initialization of the `.current` property of the ref object with an initial value.

This hook can be utilized as follows :

```jsx
import React, { useRef } from 'react';

const myRef = useRef(initialValue);
```

When `useRef()` is called, it returns a ref object, and this ref object has a `.current` property.

If no `initialValue` is provided, `myRef.current` will be null by default.

**Example 1**

In this example, the `useRef()` hook is used to store the previous value of the count state, allowing the display of the current and previous count values without triggering re-renders.

```jsx
import React, { useState, useRef, useEffect } from 'react';

function PreviousValue() {
  const [count, setCount] = useState(0);
  const prevCountRef = useRef();
  
  useEffect(() => {
    prevCountRef.current = count;
  });

  return (
    <div>
      <p>Current Count: {count}</p>
      <p>Previous Count: {prevCountRef.current}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

In the code example, useRef is used to create the `prevCountRef` reference to store and access the previous value of the count state variable.

To store the previous count value in a state variable (using `useState()`), each time the `Increment` button is clicked and the count changes, it would trigger a re-render. Because changing state variables causes components to re-render.

There is no need to re-render the component just to track the previous value; it’s only necessary to update the value without causing a re-render. So the `useRef()` hook is used.

#### useState()

The **`useState()`** hook returns the current [[#State]] of the [[#Components]] and its setter function. State must be changed through the setter only and not be mutated directly as it may cause unexpected behavior.

**Syntax**

```jsx
import React, { useState } from 'react';

const [state, setState] = useState(initialValue);
```

When the component is first rendered, its `state` is assigned an `initialValue` through the `useState()` hook. If one is not provided, the default value is `undefined`. Afterwards, the `setState` function can change the value of `state` and trigger a re-render of the component.

The `state` must be changed only through the `setState` function and not be mutated directly as this may cause unexpected behavior.

The return value of `useState()` is an array whose elements (the state and setter function) can be accessed through destructuring.

> **Note:** The name of the setter function should correlate with the name of the `state` value (e.g., `const [time, setTime] = useState(0);`).

**Example 1**

A controlled input whose value depends on the `name` state and changes it through `setName` setter on `onChange` event.

```jsx
import React, { useState } from 'react';

function ProfileName() {
  const [name, setName] = useState('');

  function handleChange(event) {
    /*
     Sets the name state to the value of
     the input after the onChange event.
    */
    setName(event.currentTarget.value);
  }

  return <input value={name} onChange={handleChange} />;
}
```

**Example 2**

In cases where the component state is an array or object, the setter function must update with a modified copy of the state, not the original.

The following example involves deleting an item from the `foods` state array and updating with `filteredItems` through the `onClick` event:

```jsx
import React, { useState } from 'react';

function FavoriteFoodList() {
  const [foods, setFoods] = useState(['pizza', 'hot dog']);

  function handleDeleteFood(deletedIndex) {
    /*
      Instead of changing the foods array directly,
      the .filter() method can be used to return a 
      copy that excludes the deleted item.
    */
    const filteredItems = foods.filter((food, index) => index !== deletedIndex);

    setFoods(filteredItems);
  }

  return (
    <div>
      {foods.map((item, index) => (
        <button onClick={() => handleDeleteFood(index)}>{item}</button>
      ))}
    </div>
  );
}
```

## JSX

JSX is a syntax extension of JavaScript that combines the JavaScript and HTML-like syntax to provide highly functional, reusable markup. It’s used to create DOM elements which are then rendered in the React DOM.

While not required in React, JSX provides a neat visual reqresentation of the application’s UI.

A JavaScript file containing JSX will have to be compiled before it reaches a web browser.

#### Syntax

JSX looks a lot like HTML:

```jsx
const headerElement = <h1>This is a header</h1>;
```

In the block of code, we see the similarities between JSX syntax and HTML: they both use the angle bracket opening `<h1>` and closing `</h1>` tags.

Under the hood, after it’s been processed to regular JavaScript, it looks like this:

```jsx
const headerElement = React.createElement('h1', 'This is a header');
```

JavaScript code, such as variables and functions, can be used in JSX, as well:

```jsx
import React from 'react';

const App = () => {
  return (
    <React.Fragment>
      <button onClick={() => 'The button was clicked!'}>Click!</button>
    </React.Fragment>
  );
};
```

#### JSX Attributes

The syntax of JSX attributes closely resembles that of HTML attributes.

```jsx
const example = <h1 id="example">JSX Attributes</h1>;
```

In the block of code, inside of the opening tag of the `<h1>` JSX element, we see an `id` attribute with the value `"example"`.

#### Nested JSX Elements

In order for the code to compile, a JSX expression must have exactly one outermost element. In the below block of code, the `<a>` tag is the outermost element.

```jsx
const myClasses = (
  <a href="https://www.twitter.com">
    <h1>Sign Up!</h1>
  </a>
);
```
#### Multiline JSX Expression

A JSX expression that spans multiple lines must be wrapped in parentheses `(` and `)`.

```jsx
const myList = (
  <ul>
    <li>Item 1</li>
    <li>Item 2</li>
    <li>Item 3</li>
  </ul>
);
```

Here, we see the opening parentheses on the same line as the constant declaration, before the JSX expression begins. We see the closing parentheses on the line following the end of the JSX expression.

#### JSX with `.map()` Method

The array method `.map()` comes up often in React. It’s good to get in the habit of using it alongside JSX.

If you want to create a list of JSX elements from a given array, then`.map()` over each element in the array, returning a list item for each one.

```jsx
const strings = ['Home', 'Shop', 'About Me'];

const listItems = strings.map((string) => <li>{string}</li>);

<ul>{listItems}</ul>;
```

#### JSX Conditionals

JSX does not support `if`/`else` syntax in embedded JavaScript. There are three ways to express conditionals for use with JSX elements:

**Using Ternary Operator**

Using ternary operator within curly braces in JSX:

```jsx
const headline = <h1>{age >= drinkingAge ? 'Buy Drink' : 'Do Teen Stuff'}</h1>;
```

**Using `if` Statement**

Using `if`/`else` statement outside of JSX element:

```jsx
let text;

if (age >= drinkingAge) {
  text = 'Buy Drink';
} else {
  text = 'Do Teen Stuff';
}

const headline = <h1>{text}</h1>;
```

**Using `&&` Operator**

Using `&&` AND operator:

```jsx
// Renders as empty div if length is 0
const unreadMessages = ['hello?', 'remember me!'];

const update = (
  <div>
    {unreadMessages.length > 0 && (
      <h1>You have {unreadMessages.length} unread messages.</h1>
    )}
  </div>
);
```

## Lifecycle Methods

In React, **lifecycle methods** are unique event listeners that listen for changes during certain points during a [[#Components]]‘s lifecycle. A component’s lifecycle usually runs in this order:

1. Rendering/mounting a component to the DOM for the first time.
2. Updating an existing component.
3. Catching any errors associated with a component render-gone-wrong.
4. Unmounting a component and removing it from the DOM.

Lifecycle methods were originally exclusive to class components. But thanks to React [[#Hooks]], even functional component’s can work with their lifecycle.

#### Rendering/Mounting a Component

After a component is first created and `render()`ed to the DOM, it is “mounted”. The method used here is called `componentDidMount()`, which occurs after the component is rendered.

```jsx
import React from "react";

class App extends React.Component {
  componentDidMount() {
    console.log("Component has mounted!")
  }

  render() {
    console.log("Rendering...")
    return(
      <React.Fragment>
      <React.Fragment>
    )
  }
}
```

And the output should be this:

```sh
Rendering...
Component has mounted!
```
#### Updating a Component

When changes are made to a component, usually to its `state`, it is in the update-phase of its lifecycle. This is handled by the `componentDidUpdate()` method.

Using the common `<Counter>` example, and some custom `increase()` and `decrease()` event methods to change its `count` state, `compomentDidUpdate()` will execute immediately after either of these methods executes:

```jsx
import React from 'react';

class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0,
    };
    this.increment = () => this.setState({ count: this.state.count + 1 });
    this.decrement = () => this.setState({ count: this.state.count - 1 });
  }

  componentDidMount() {
    console.log('Component has mounted.');
  }

  componentDidUpdate() {
    console.log('Component updated. Count is now ' + this.state.count + '.');
  }

  render() {
    console.log('Rendering...');
    return (
      <React.Fragment>
        <h1>Count: {this.state.count}</h1>
        <button onClick={this.increment}>+1</button>
        <button onClick={this.decrement}>-1</button>
      </React.Fragment>
    );
  }
}
```

Assuming we clicked the “+” button twice, which causes a re-`render()` each time, our output would be this:

```sh
Rendering...
Component has mounted.
Rendering...
Component updated. Count is now 1.
Rendering...
Component updated. Count is now 2.
```

#### Catching Errors in Components

Prior to React 16, there were no features that handled errors being thrown during the component tree’s “rendering” phase. When this occurred, the following would happen:

- The entire app would crash and all components would ummount.
- A trace report of the error would replace the UI.

This prompted the creation of the following lifecycle methods for handling errors in React class components:

- `static getDerivedStateFromError(error)` for returning an updated state to allow a render of fallback UI.
- `componentDidCatch(error, errorInfo)` for logging error information to the console, including a component stack tracing back to the source of the error.

Any class component that uses either or both of these methods is considered to be an [Error Boundary component](https://reactjs.org/docs/error-boundaries.html) because they are catching errors thrown from their child components.

```jsx
import React from "react";

class CounterThatThrowsError extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      count: 0
    }

    this.increment = () => this.setState({count: this.state.count + 1})
    this.decrement = () => this.setState({count: this.state.count - 1})
  }

  render() {
    if(this.state.count === 3) throw new Error("Crashed!")
    return(
      <React.Fragment>
        <h1>Count: {this.state.count}</h1>
        <button onClick={this.increment}>+1</button>
        <button onClick={this.decrement}>-1</button>
      </React.Fragment>
    )
  }
};

class App extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      hasError: false,
      error: null
    }
  }

  getDerivedStateFromError(error) {
    return {
      hasError: true,
      error: error
    }
  }

  componentDidCatch(error, errorInfo) {
    console.log(error.name + ": " error.message)
    // Output: Error: Crashed!
  }

  render() {
    if(this.state.hasError) {
      return (
        <div style={{ marginLeft: "2em" }}>
          <h1>Oh no! Something's gone wrong!</h1>
        </div>
      )
    }

    return(
      <div className="App">
        <Counter />
      </div>
    )
  }
}
```

> **Note:** `getDerivedStateFromError()` and `componentDidCatch()` are only able to work with errors strictly related to their child components. They are not compatible with errors related to event handlers.

#### Unmounting a Component

When a component is unmounted, it is considered to be at the end of its lifecycle within the React application. The associated lifecycle method is `componentWillUnmount()`, which executes when React anticipates its host-component will be destroyed.

Going back to the previous example where the `<Counter>` component throws an error after is state is equal to 3, it is thereby destroyed and replaced by new a new UI inside of its parent component, `<App>`.

```jsx
import React from "react";

class CounterThatThrowsError extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      count: 0
    }

    this.increment = () => this.setState({count: this.state.count + 1})
    this.decrement = () => this.setState({count: this.state.count - 1})
  }

  componentWillUnmount() {
    console.log("Counter has been unmounted!")
    // Output: Counter has been unmounted!
  }

  render() {
    if(this.state.count === 3) throw new Error("Crashed!")
    return(
      <React.Fragment>
        <h1>Count: {this.state.count}</h1>
        <button onClick={this.increment}>+1</button>
        <button onClick={this.decrement}>-1</button>
      </React.Fragment>
    )
  }
};

class App extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      hasError: false,
      error: null
    }
  }

  getDerivedStateFromError(error) {
    return {
      hasError: true,
      error: error
    }
  }

  componentDidCatch(error, errorInfo) {
    console.log(error.name + ": " error.message)
    // Output: Error: Crashed!
  }

  render() {
    if(this.state.hasError) {
      return (
        <div style={{ marginLeft: "2em" }}>
          <h1>Oh no! Something's gone wrong!</h1>
        </div>
      )
    }

    return(
      <div className="App">
        <Counter />
      </div>
    )
  }
}
```

## Props

In React, components are able to use props, or “properties”, to display and share data throughout the application. In other words, props is the information that gets passed from one component to another.

Parent components can pass props to their child components, but not the other way around. Props can be many data types, including:

- Numbers
- Strings
- Functions
- Objects

#### Syntax

```jsx
import React from 'react';

class ParentComponent extends React.Component {
  render() {
    return <ChildComponent prop1="Mike" prop2="piza">
  }
}

function ChildComponent(props) {
  return <h2>This is prop1: {props.prop1}. This is prop2: {props.prop2}.</h2>
}
```

#### `this.props`

Every component has something called `props`.

A component’s `props` is an object. It holds information about that component.

To see a component’s `props` object, you use the expression `this.props`. Here’s an example of `this.props` being used inside of a render method:

```jsx
render() {
  // Printing the props object
  console.log(this.props);
  return <h1>Hello world</h1>;
}
```

#### Pass `props` to a Component

You can pass information to a React component. How? By giving that component an attribute:

```jsx
<MyComponent foo="bar" />
```

Let’s say that you want to pass a component the message, `"This is some top secret info."`. Here’s how you could do it:

```jsx
<Example message="This is some top secret info." />
```

As you can see, to pass information to a component, you need a name for the information that you want to pass.

In the above example, we used the name `message`. You can use any name you want.

If you want to pass information that isn’t a string, then wrap that information in curly braces. Here’s how you would pass an array:

```jsx
<Greeting myInfo={['top', 'secret', 'lol']} />
```

In this next example, we pass several pieces of information to `<Greeting />`. The values that aren’t strings are wrapped in curly braces:

```jsx
<Greeting name="Frarthur" town="Flundon" age={2} haunted={false} />
```

#### Displaying the Props

You will often want a component to display the information that you pass.

Here’s how to make a component display passed-in information:

1. Find the component class that is going to receive that information.
2. Include `this.props.name-of-information` in that component class’s render method’s `return` statement.

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

class Greeting extends React.Component {
  render() {
    return <h1>Hi there, {this.props.firstName}!</h1>;
  }
}

ReactDOM.render(<Greeting firstName="Rybu" />, document.getElementById('app'));
```

## React Native

[**React Native**](https://reactnative.dev/) is an open-source mobile development framework that works across different operating systems such as iOS and Android. It was released in 2015 by Facebook (now Meta) and can be used on many smart devices, such as phones, personal computers, and TVs, to provide a consistent interface and user experience.

Traditional mobile app development required multiple codebases for each operating system along with their associated programming languages (e.g., Swift for iOS or Java or Kotlin for Android). However, React Native provides a single codebase written in [[#React]]-based JavaScript and rendered in the device’s native language.

#### Getting Started

React Native can be installed, configured, and used in two ways:

- The [Expo CLI](https://reactnative.dev/docs/environment-setup) only requires Node.js to quicky build and troubleshoot an application. This can also be done in-browser with [Snack](https://snack.expo.dev/).
- The [React Native CLI](https://reactnative.dev/docs/environment-setup#installing-dependencies) requires a few extra dependencies and configurations, depending on the scope of the project.

[[How to React Native]] describes the appearance and behavior of the UI of a mobile application.

## Routing

In React, routing is most commonly achieved through React Router.

React Router is a third-party library that allow components to be used as routed endpoints within an application. This library comes in three packages:

- `react-router`: For core functionality
- `react-router-native`: React Native version
- `react-router-dom`: Recommended for web applications

Install the newest version with `npm`:

```sh
npm install react-router-dom@latest
```

The newest version of React Router is [v6](https://reactrouter.com/en/6.10.0/start/overview), which comes with many breaking changes including:

- Using a `<Routes />` component instead of a `<Switch />` component for `<Route />` configuration.
- Using the `element` attribute instead of `component` when passing a JSX to a `<Link />`.

#### Example

Below, a `<BrowserRouter/>` (aliased as `<Router/>`) is used to map the navigation `<Link/>` components to a `<Routes/>` component. The `<Routes/>` renders the appropriate `<Route>` based on the most specific-matching `path` among all possible matches.

```jsx
import Home from './Home';
import About from './About';
import Projects from './Projects';
import Contact from './Contact';
import { BrowserRouter as Router, Routes, Route, Link } from 'react-router-dom';

const App = () => {
  return (
    <div>
      <Router>
        <nav>
          <ul>
            <Routes>
              <Route path="/about" element={<About />} />
              <Route path="/blog" element={<Blog />} />
              <Route path="/contact" element={<Contact />} />
              <Route path="/" element={<Home />} />
            </Routes>
            <li>
              <Link to="/">Home</Link>
            </li>
            <li>
              <Link to="/about">About</Link>
            </li>
            <li>
              <Link to="/projects">Projects</Link>
            </li>
            <li>
              <Link to="/contact">Contact</Link>
            </li>
          </ul>
        </nav>
      </Router>
    </div>
  );
};

const Home = () => {
  return <h2>Home</h2>;
};

const About = () => {
  return <h2>About</h2>;
};

const Projects = () => {
  return <h2>Projects</h2>;
};

const Contact = () => {
  return <h2>Contact</h2>;
};

export default App;
```

## State

The **`state`** object holds data in a [[#Components]] that is expected to change over time. It is created and maintained by the component itself, and when it changes, the component re-renders.

[[#Props]] are passed down by parent components, whereas state is created and maintained by the component itself.

Class and functional components handle state differently. Functional components use [[#Hooks]] to manage state. The following will address how class components manage state.

#### Syntax

```jsx
class ComponentName extends React.Component {
  // With constructor
  constructor(props) {
    super(props);
    this.state = {
      property: value,
    };
  }

  // With property initializer
  state = {
    property: value,
  };

  render() {
    return (
      /* Value of property is rendered */
      <div>{this.state.property}</div>
    );
  }
}
```

The snippet above features two common ways of initializing state in a class component:

- The `constructor()` method assigns a new object to `this.state`.
- A `state` object is directly assigned with property initializers.

Only one of these two ways can be applied when making a class component.

#### Example

The following example initializes the `state` object in the component’s `constructor()` and returns its value in the `render()` method:

```jsx
class Car extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      brand: 'Chevrolet',
      model: 'Malibu',
      color: 'white',
      year: 1998,
    };
  }

  render() {
    return (
      <div>
        <h1>My First Car</h1>
        <p>
          It is a {this.state.color}
          {this.state.brand}
          {this.state.model}
          made in {this.state.year}. 🚙
        </p>
      </div>
    );
  }
}
```
With property initializers, it is not necessary to set state or bind methods in the constructor:

```jsx
class Car extends React.Component {
  // No need to use constructor
  state = {
    brand: 'Chevrolet',
    model: 'Malibu',
    color: 'white',
    year: 1998,
  };
  
  // Same renderd JSX
}
```

## Virtual DOM

In React, **virtual DOM** is a conceptual representation of the actual DOM object, like a lightweight copy. A virtual DOM object has the same properties as a real DOM object, but it lacks the real thing’s power to directly change what’s on the screen.

Manipulating the DOM is slow. Manipulating the virtual DOM is much faster, because nothing gets drawn onscreen. Think of manipulating the virtual DOM as editing a blueprint, as opposed to moving rooms in an actual house.

With virtual DOM, when changes are made to the UI and there’s a re-render, they are compared to the relevant branches in the DOM-tree. Then, the changes are applied to the DOM-tree.

![[Pasted image 20240511160800.png]]

The virtual DOM is not to be confused with the Shadow DOM, or any other technology or concept.

#### How It Works

During a render of a [[#JSX]] element, every single virtual DOM object gets updated. This sounds incredibly inefficient, but the cost is insignificant because the virtual DOM can update so quickly. Once the virtual DOM has updated, then React compares the virtual DOM with a virtual DOM snapshot that was taken right before the update.

By comparing the new virtual DOM with a pre-update version, React figures out exactly which virtual DOM objects have changed. This process is called “diffing.”

Once React knows which virtual DOM objects have changed, then React updates those objects, and only those objects, on the real DOM. For example, if changes were made to a list item, React would know to rebuild the one checked-off list item, and leave the rest of the list alone.

This makes a big difference! React can update only the necessary parts of the DOM. React’s reputation for performance comes largely from this innovation.

In summary, here’s what happens trying to update the DOM in React:

1. The entire virtual DOM gets updated.
2. The virtual DOM gets compared to what it looked like before it was updated. React figures out which objects have changed.
3. Only the objects that were changed get updated on the real DOM.
4. Changes on the real DOM cause the screen to change.