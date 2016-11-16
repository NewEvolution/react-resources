# React Basics

Before starting, it's good to get a grasp of what React is and isn't.  React is _only_ the V in MVC.  It's a modular component-driven way to get dynamic data into the DOM, and does not concern itself with where that data comes from, or how it got there.

## Components

React is made up of units called components. Simple components can be written in function syntax.  They just return what they render to the DOM:

```javascript
function Hello() {
  return(
    <h1>Hello World!</h1>
  );
}
```

More complex components can be written in class syntax.  They _must_ have a `render` function which returns what they render to the DOM:
```javascript
class Hello extends React.Component {
  render() {
    return(
      <h1>Hello World!</h1>
    );
  }
}
```

## JSX

React is generally written in JSX, short for JavaScript [XML](https://en.wikipedia.org/wiki/XML).  React can be written as plain JavaScript, but JSX's syntax and structure is generally more intutive and easier to follow. Before runtime the JSX is [transpiled](https://en.wikipedia.org/wiki/Source-to-source_compiler) into JavaScript that can be uderstood by the browser.

The X part of JSX comes into play within the return value of function-style components, and within the return value of the render function of class-style components.  Though you may not have written much XML, with JSX there's not a huge difference from the HTML you're used to writing.

One caveat is that in XML all tags must be explicitly closed.  For self-closing tags like `<input>` and `<img>`, the tag must have the closing slash included i.e. `<input />` `<img />`.  HTML tags render into the DOM as you would expect.  Components defined in your scripts can be included in other components by treating their function/class names as XML tags.  Here the `Hello` component is included within the `HelloHolder` component:
```javascript
function Hello() {
  return (
    <h2>Hello World!</h2>
  );
}

function HelloHolder() {
  return(
    <div>
      <h1>Welcome Developers</h1>
      <Hello />
    </div>
  );
}
```

## Rendering
In React, the components build the view, but need a way to be attached to the DOM.  This is accomplished with the `ReactDOM.render` function.  You'll generally only have one of these for your page, and it takes as arguments the React component to render, and the DOM element to render it to:
```javascript
class HelloHolder extends React.Component {
  render() {
    return(
      <Hello World! />
    );
  }
}

ReactDOM.render(<HelloHolder />, document.getElementById('content'));
```
```html
<head>
  <title>React Basics</title>
</head>
<body>
  <div id="content">
    <!-- replaced by React -->
  </div>
</body>
```
## Props

Components can contain other components and pass information into them as `props`.
```javascript
function HelloTile(props) {
  return(
    <h1>Hello {props.name}!</h1>
  );
}

class TileHolder extends React.Component {
  render() {
    return(
      <HelloTile name={'World'} />
    );
  }
}
```
## State

Components have an internal data storage object called `state`.  Changes to the `state` cause a re-render of the page, and those changes can be passed down to child components via their `props`.
```javascript
function HelloTile(props) {
  return(
    <h1>Hello {props.name}!</h1>
  );
}

class TileHolder extends React.Component {
  constructor() {
    super();
    this.state = {
      name: 'World'
    };
  }
  render() {
    return(
      <HelloTile name={this.state.name} />
    );
  }
}
```

While all components can have a `state`, a `state` that affects multiple children should be contained in the parent component that contains all children that need the state data.

Components should only ever modify _their own_ state directly.  Children should use functions passed in as `props` from their parents to modify that parent's state.
```javascript
function HelloTile(props) {
  return(
    <h1>Hello {props.name}!</h1>
  );
}

function NameInput(props) {
  return(
    <input
      type='text'
      onChange={e => props.changeName(e)}
      placeholder='World'
    />
  );
}

class TileHolder extends React.Component{
  constructor() {
    super();
    this.state = {
      name: 'World'
    };
  }
  changeName(e) {
    this.setState({
      name: e.target.value
    });
  }
  render() {
    return(
      <div>
        <HelloTile name={this.state.name} />
        <NameInput changeName={e => this.changeName(e)} />
      </div>
    );
  }
}
```

## ES6 Powerup
Lets step it up a notch and bind `this` to our function to eliminate the repeated lambdas, use destructuring to clean up our props, and turn our simple components into lambda functions.
```javascript
const HelloTile = ({name}) => <h1>Hello {name}!</h1>,
      NameInput = ({changeName}) =>
        <input
          type='text'
          onChange={e => changeName(e)}
          placeholder='World'
        />;

class TileHolder extends React.Component {
  constructor() {
    super();
    this.state = {
      name: 'World'
    };
    this.changeName = this.changeName.bind(this);
  }
  changeName(e) {
    this.setState({
      name: e.target.value
    });
  }
  render() {
    return(
      <div>
        <HelloTile name={this.state.name} />
        <NameInput changeName={this.changeName} />
      </div>
    );
  }
}
```

### Further Reading

- [React's Official Tutorial](https://facebook.github.io/react/tutorial/tutorial.html) - A simple tic-tac-toe game utilizing state, props & components.
- [Tyler McGinnis' React Tutorial](https://tylermcginnis.com/react-js-tutorial-pt-1-a-comprehensive-guide-to-building-apps-with-react-js-8ce321b125ba#.8njkv6hwm) - In-depth exploration of React's place in the web development pantheon & the inner workings of data flow through React components.
