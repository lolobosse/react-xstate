# react-xstate
Connecting react components with [xstate](https://github.com/davidkpiano/xstate) state machine library.

# Why?

Comments Lolo:
* Why would I need your xstate?
* Describe the problems better
* Link to the white paper of xstate
* Link to xstate
* I can do state handling in different file with modules as well: go deeper, explain the pain and why it really solves it. If you cannot it's because you didn't understand deep enough :wink:
* What do you do more than xstate, what value brings your additional complexity?

Gives you easy access to xstate in the react world and allows you to improve your code structure by extracting state handling into different files.

# Installation
1. ``npm install react-xstate --save``
2. ``import { mountXstate } from 'react-xstate'``

# Usage
Mount the xstate machine to your component by applying a `machine` definition and at least one `actionReducer` to your component.

```js
mountXstate(appMachine, [appReducer])(App)
```

## Example
This simple state machine implements an easy to use statechart that transitions between ping and pong and when you click in state ping you will trigger the `consoleLog` action.

![State Machine](ppmachine.png "Ping Pong State Machine")

#### State Machine
````js
const appMachine = {
  initial: 'pending',
  states: {
    ping: {
      on: {
        CLICK: {
          pong: { actions: ['consoleLog'] }
        }
      }
    },
    pong: {
      on: {
        CLICK: 'ping',
      }
    },
  }
}
````

#### Action Reducer
````js
const appReducer = (action, event, xstate) => {
  if(action === 'consoleLog') {
    console.log('Fired action: consoleLog')
  }
}
````

#### Stateful Component

````js
class App extends Component {
  constructor(props) {
    super(props);
    this.handleSubmit = this.handleSubmit.bind(this);
  }
  handleSubmit() {
    // See transition definition prop below  
    const { transition } = this.props
    // Wtf is that? The payload of the Action?
    transition({ type: 'CLICK' })
  }
  render() {
    const { xstate: { value: state } } = this.props
    console.log(`State: ${JSON.stringify(state)}`)
    return (
      <button onClick={this.handleSubmit}>Click</button>
    );
  }
}

export default mountXstate(appMachine, [appReducer])(App)
````

# Prop definition
The higher order component `mountXstate` will add and expose two new props to your component:

1. **xstate** prop which exposes the [state](http://davidkpiano.github.io/xstate/docs/#/api/state) of the state machine to enable the user to build statefull component logic
2. **transition** prop which exposes the transition function to fire events towards your state machine.

