# Components

React is made up of units called components. Simple components can be written in function syntax:

```xml
function Hello(props) {
  return(
    <h1>Hello World!</h1>
  )
}
```

And more complex ones can be written in class syntax:
```xml
class StateHello extends React.Component{
  constructor() {
    super();
    this.state = {
      name: 'World'
    }
  }
  render() {
    return(
      <h1>Hello {this.state.name}!</h1>
    )
  }
}
```

Components can contain other components and pass information into them as props.
```xml
function HelloTile(props) {
  return(
    <h1>Hello {props.name}!</h1>
  )
}

class TileHolder extends React.Component{
  constructor() {
    super();
    this.state = {
      name: 'World'
    }
  }
  render() {
    return(
      <ChangeableTile name={this.state.name} />
    )
  }
}
```
