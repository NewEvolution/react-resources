# React Basics

## Components

React is made up of units called components. Simple components can be written in function syntax.  They just return what they render to the DOM:

```javascript
function Hello() {
  return(
    <h1>Hello World!</h1>
  )
}
```

And more complex ones can be written in class syntax.  They _must_ have a `render` function which returns what they render to the DOM:
```javascript
class Hello extends React.Component {
  render() {
    return(
      <h1>Hello World!</h1>
    )
  }
}
```
## Props

Components can contain other components and pass information into them as `props`.
```javascript
function HelloTile(props) {
  return(
    <h1>Hello {props.name}!</h1>
  )
}

class TileHolder extends React.Component {
  render() {
    return(
      <HelloTile name={'World'} />
    )
  }
}
```
## State

Components have an internal data storage object called `state`.  Changes to the `state` cause a re-render of the page, and those changes can be passed down to child components via their `props`.
```javascript
function HelloTile(props) {
  return(
    <h1>Hello {props.name}!</h1>
  )
}

class TileHolder extends React.Component {
  constructor() {
    super();
    this.state = {
      name: 'World'
    }
  }
  render() {
    return(
      <HelloTile name={this.state.name} />
    )
  }
}
```

While all components can have a `state`, a `state` that affects multiple children should be contained in the parent component that contains all children that need the state data.

Components should only ever modify _their own_ state directly.  Children should use functions passed in as `props` from their parents to modify that parent's state.
```javascript
function HelloTile(props) {
  return(
    <h1>Hello {props.name}!</h1>
  )
}

function NameInput(props) {
  return(
    <input
      type='text'
      onChange={e => props.changeName(e)}
      placeholder='World'
    />
  )
}

class TileHolder extends React.Component{
  constructor() {
    super();
    this.state = {
      name: 'World'
    }
  }
  changeName(e) {
    this.setState({
      name: e.target.value
    })
  }
  render() {
    return(
      <div>
        <HelloTile name={this.state.name} />
        <NameInput changeName={e => this.changeName(e)}
      </div>
    )
  }
}
```


