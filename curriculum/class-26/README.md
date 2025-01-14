# Class 26 - Application State

## Learning Objectives

* Manage state at an application level using a Redux Store
* Understand the basic roles of Actions and Reducers
* Implement the core Redux Boilerplating required to participate in a store-based state managed application

## Outline
* :05 **Housekeeping/Recap**
* :30 **Whiteboard/DSA Review**
* :15 **Lightning Talk**
* Break
* :30 **CS/UI Concepts** -
* :20 **Code Review**
* Break
* :60 **Main Topic**

## UI Concept:
* Component Styling
* `<Header />` and `<Footer />` components

## Main Topic:
* Global State
* Redux Stores
* Actions and Reducers
* **[DEMO](https://codesandbox.io/s/2p30m03o9n)** (explained below)

**Store**

Ultimately, the "store" is where your application state is, well, stored.  The store's job is to identify the various reducers and middleware that need to be made available and used globally.

React uses "reducers" to hold and manage state. Reducers typically manage just one part of the larger application state.  For example, in a storefront application, you would likely have a separate reducer for Products, Categories, and Carts

Here's a sample reducer for a shopping cart. As you can see it creates an initial "empty" state, and then identifies what todo when a certain action (e.g. INITIALIZE) is called. Reducers always "Hear" that an action was dispatched, and use whatever "payload" they receive to do their work.

```javascript
let initialState = { customerId: null, items: [] };

const myReducer = (state = initialState, action) => {
  let { type, payload } = action;

  switch (type) {
    case 'INITIALIZE': 
      return {customerId: payload.id};
      
    case 'ADD_ITEM':
      return { items: [...items, payload.item] };
      
    case 'CLEAR':
      return initialState;

    default:
      return state;
  }
};
```

React applications with Redux dispatch "actions" (like an event) with "payload" (data). An action creator function as shown below always returns an action object with the action type to perform and the data to perform it with.  When your component wants to modify state, it "Dispatches" (calls) an action and sends whatever payload (data) it needs to, to the reducer.

When an action is dispatched, a reducer responds to it, and receives that payload, where it then operates on state using it.

```javascript
// actions.js
const newCart = customer => {
  return {
    type: 'INITIALIZE',
    payload: customer,
  };
};

```

We use a store to "bring it all together" ... in the store, you declare what middleware you may need and the reducers that you'll use to manage your state data

```javascript
import { createStore, combineReducers, applyMiddleware } from 'redux';

import cartReducer from './reducers/cart.js';

let reducers = combineReducers({
  cart: cartReducer,
});

export default () => createStore(reducers);
```

Components subscribe to the store and get to use actions with a bit of boilerplate code ...

In this example, when the 'Start Shopping' button is clicked, it runs a method called `this.props.initializeTheCart()`. 

This method is declared in the `mapDispatchToProps` function as a pointer to the `newCart()` method in the actions file. 

When you do this, you get to use `this.props.initializeTheCart` as a method. That's what the mapper does for you, along with mapping the reducers's state to `this.props` with the key that you specified.


```javascript
import React from 'react';
import { connect } from 'react-redux';

import * as actions from '../store/actions.js';

class App extends React.Component {
  constructor(props) {
    super(props);
  }

  render() {
    return (
      <button onClick={this.props.initializeTheCart}>
        Start Shopping!
      </button>
    );
  }
}

const mapStateToProps = state => ({
  cart: state.cart,
});

const mapDispatchToProps = (dispatch, getState) => ({
  initializeTheCart: () => dispatch(actions.newCart()),
});

export default connect(
  mapStateToProps,
  mapDispatchToProps,
)(App);

```

Before any of this can work, your entire application needs to be given access to the store. Your component (above) uses the redux `connect()` method to attach to the store, but without first "Providing" it to your application, that connection will fail.  The provider wrapper is a means that React uses to allow child components to have access to higher level context.


```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';

import './style.scss';

import App from './components/app';

import createStore from './store';
const store = createStore();

class Main extends React.Component {
  render() {
    return (
      <Provider store={store}>
        <React.Fragment>
          <App />
        </React.Fragment>
      </Provider>
    );
  }
}

const rootElement = document.getElementById('root');
ReactDOM.render(<Main />, rootElement);
```
