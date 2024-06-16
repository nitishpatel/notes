---
draft: false
date: 2023-01-31
slug: redux-toolkit
categories:
  - React
  - Redux
tags:
  - redux
---

# What is Redux?
Redux is a predictable state container for JavaScript apps


## Setup

```bash
mkdir reduxtoolkit
npm init -y
```

Install the Redux Package
```bash
npm install redux
```


## Redux Core Concepts

- A **store** that holds the state of your application
- An **Action** that describes what happened in the application
- A **Reducer** which handles the action and decides how to update the state

## Redux Principles

- **First Principle**
*The global state of your application is stored as an object inside a single store.*
Maintain your application state in a single object which would be managed by the Redux Store

- **Second Principle**
*The only way to change the state is to dispatch an action, an object that describes what happened.*
To update the state of your app, you need to let redux know about that with an action i.e Not allowed to directly update the state object.

- **Third Principle**
*To specify how the state tree is updated based on actions, you write pure reducers.*
Reducer - (previousState,action) => newState

# Basic Redux

## Creating an Action
```js
const CAKE_ORDERED = "CAKE_ORDERED";

function orderCake() {
  return {
    type: CAKE_ORDERED,
    quantity: 1,
  };
}
```

## Creating an Reducer

```js
const initialState = {
  numberOfCakes: 10,
};

const reducer = (state = initialState, action) => {
  switch (action.type) {
    case CAKE_ORDERED:
      return {
        ...state,
        numberOfCakes: state.numberOfCakes - 1,
      };
    default:
      return state;
  }
};

```

## Creating a Redux Store

Responsibilities 
- Holds the application state
- Allows access to state via ***getState()***
- Allows state to be updated via ***dispatch(action)***
- Registers listeners via ***subscribe(listener)***
- Handles unregistering of listeners via the function returned by ***subscribe(listener)***

```js
const redux = require("redux");

const createStore = redux.createStore;


const store = createStore(reducer);
console.log("Initial state", store.getState());

const unsubscribe = store.subscribe(() =>
  console.log("Updated state", store.getState())
);

store.dispatch(orderCake());

store.dispatch(orderCake());

store.dispatch(orderCake());

unsubscribe();

```

## Creating Multiple Reducers and Combining them

In the initial scenario we only had one Reducer which was related to the cake store, now we will add the ice cream store, this can be done using a single Reducer like below 

### Single Reducer

```js
const redux = require("redux");
const createStore = redux.createStore;
const bindActionCreators = redux.bindActionCreators;

// Actions
const CAKE_ORDERED = "CAKE_ORDERED";
const CAKE_RESTOCKED = "CAKE_RESTOCKED";
const ICECREAM_ORDERED = "ICECREAM_ORDERED";
const ICECREAM_RESTOCKED = "ICECREAM_RESTOCKED";

function orderCake() {
  return {
    type: CAKE_ORDERED,
    payload: 1,
  };
}

function restockCake() {
  return {
    type: CAKE_RESTOCKED,
    payload: 20,
  };
}

function orderIceCream(qty = 1) {
  return {
    type: ICECREAM_ORDERED,
    payload: qty,
  };
}

function restockIceCream(qty = 1) {
  return {
    type: ICECREAM_RESTOCKED,
    payload: qty,
  };
}

// (previousState, action) => newState;
const initialState = {
  numberOfCakes: 10,
  numberOfIceCreams: 10,
};

const reducer = (state = initialState, action) => {
  switch (action.type) {
    case CAKE_ORDERED:
      return {
        ...state,
        numberOfCakes: state.numberOfCakes - 1,
      };
    case CAKE_RESTOCKED:
      return {
        ...state,
        numberOfCakes: action.payload,
      };
    case ICECREAM_ORDERED:
      return {
        ...state,
        numberOfIceCreams: state.numberOfIceCreams - action.payload,
      };
    case ICECREAM_RESTOCKED:
      return {
        ...state,
        numberOfIceCreams: state.numberOfIceCreams + action.payload,
      };
    default:
      return state;
  }
};


const store = createStore(reducer);
console.log("Initial state", store.getState());

const unsubscribe = store.subscribe(() =>
  console.log("Updated state", store.getState())
);

const actions = bindActionCreators(
  { orderCake, restockCake, orderIceCream, restockIceCream },
  store.dispatch
);
actions.orderCake();
actions.orderCake();
actions.orderCake();

actions.restockCake();
actions.orderIceCream();
actions.orderIceCream();
actions.restockIceCream();
unsubscribe();

```

### Multiple Reducers
In this case the dispatch will be sent to both the reducers but only the reducer which is meant to handle the action will take the action and update the state.
```js
const redux = require("redux");
const createStore = redux.createStore;
const bindActionCreators = redux.bindActionCreators;
const combineReducers = redux.combineReducers;


const CAKE_ORDERED = "CAKE_ORDERED";
const CAKE_RESTOCKED = "CAKE_RESTOCKED";
const ICECREAM_ORDERED = "ICECREAM_ORDERED";
const ICECREAM_RESTOCKED = "ICECREAM_RESTOCKED";

function orderCake() {
  return {
    type: CAKE_ORDERED,
    payload: 1,
  };
}

function restockCake() {
  return {
    type: CAKE_RESTOCKED,
    payload: 20,
  };
}

function orderIceCream(qty = 1) {
  return {
    type: ICECREAM_ORDERED,
    payload: qty,
  };
}

function restockIceCream(qty = 1) {
  return {
    type: ICECREAM_RESTOCKED,
    payload: qty,
  };
}

// (previousState, action) => newState;
const initialState = {
  numberOfCakes: 10,
  numberOfIceCreams: 10,
};

const cakeReducer = (state = initialState, action) => {
  switch (action.type) {
    case CAKE_ORDERED:
      return {
        ...state,
        numberOfCakes: state.numberOfCakes - 1,
      };
    case CAKE_RESTOCKED:
      return {
        ...state,
        numberOfCakes: action.payload,
      };
    default:
      return state;
  }
};
const iceCreamReducer = (state = initialState, action) => {
  switch (action.type) {
    case ICECREAM_ORDERED:
      return {
        ...state,
        numberOfIceCreams: state.numberOfIceCreams - action.payload,
      };
    case ICECREAM_RESTOCKED:
      return {
        ...state,
        numberOfIceCreams: state.numberOfIceCreams + action.payload,
      };
    default:
      return state;
  }
};

// we combine the reducer here
const rootReducer = combineReducers({
  cake: cakeReducer,
  iceCream: iceCreamReducer,
});

const store = createStore(rootReducer);
console.log("Initial state", store.getState());

const unsubscribe = store.subscribe(() =>
  console.log("Updated state", store.getState())
);

const actions = bindActionCreators(
  { orderCake, restockCake, orderIceCream, restockIceCream },
  store.dispatch
);
actions.orderCake();
actions.orderCake();
actions.orderCake();

actions.restockCake();
actions.orderIceCream();
actions.orderIceCream();
actions.restockIceCream();
unsubscribe();

```

## Working with Nested States

## Generic Approach
While working with nested states a developer might need to properly destructure the state while updating it. 
For Example:

**Initial State**

```js
const initialState = {
	name:'Red Velvet',
	details:{
		price:499,
		type:'Cheese Cake',
		size:'Medium'
	}
};
```

The ideal way to update this state using the reducer would be as follows:
```js
const reducer = (state = initialState, action) => {
  switch (action.type) {
    case UPDATE_PRICE:
      return {
        ...state,
        details: {
          ...state.details,
          price: action.payload,
        },
      };
    default:
      return state;
  }
};

```

This is completely doable however the developer needs to take care of carefully destructing the state for all the nested updates being made.

# Using Immer
The immer library comes in handy to handle the above situation

***Install immer***
```bash
npm install immer
```

**Updated Code**

```js
const produce = require("immer").produce;

const reducer = (state = initialState, action) => {
  switch (action.type) {
    case UPDATE_PRICE:
      return produce(state, (draftState) => {
        draftState.details.price = action.payload;
      });
    default:
      return state;
  }
};
```

# Redux Middle ware

For now we will be integrating redux-logger

## Redux Logger

**Install Redux Logger**

```bash
npm install redux-logger
```

Add the following to your code
```js
const applyMiddleware = redux.applyMiddleware;
const reduxLogger = require("redux-logger");

const logger = reduxLogger.createLogger();

// Apply the logger middleware while creating the store
const store = createStore(rootReducer, applyMiddleware(logger));


```

Output of Logger 
```bash
Initial state {
  cake: { numberOfCakes: 10, numberOfIceCreams: 10 },
  iceCream: { numberOfCakes: 10, numberOfIceCreams: 10 }
}
 action CAKE_ORDERED @ 23:33:18.538
   prev state {
    cake: { numberOfCakes: 10, numberOfIceCreams: 10 },
    iceCream: { numberOfCakes: 10, numberOfIceCreams: 10 }
  }
   action     { type: 'CAKE_ORDERED', payload: 1 }
   next state {
    cake: { numberOfCakes: 9, numberOfIceCreams: 10 },
    iceCream: { numberOfCakes: 10, numberOfIceCreams: 10 }
  }
```

# Redux Async Actions
Install Redux Thunk

```bash
npm install redux-thunk
```

```js
const redux = require("redux");
const applyMiddleware = redux.applyMiddleware;
const createStore = redux.createStore;
const thunkMiddleware = require("redux-thunk").default;
const axios = require("axios");
const initialState = {
  loading: false,
  users: [],
  error: "",
};

// Action Types

const FETCH_USERS_REQUEST = "FETCH_USERS_REQUEST";
const FETCH_USERS_SUCCESS = "FETCH_USERS_SUCCESS";
const FETCH_USERS_FAILURE = "FETCH_USERS_FAILURE";

// Action Creators

const fetchUsersRequest = () => {
  return {
    type: FETCH_USERS_REQUEST,
  };
};

const fetchUsersSuccess = (users) => {
  return {
    type: FETCH_USERS_SUCCESS,
    payload: users,
  };
};

const fetchUsersFailure = (error) => {
  return {
    type: FETCH_USERS_FAILURE,
    payload: error,
  };
};

// Reducer

const reducer = (state = initialState, action) => {
  switch (action.type) {
    case FETCH_USERS_REQUEST:
      return {
        ...state,
        loading: true,
      };
    case FETCH_USERS_SUCCESS:
      return {
        loading: false,
        users: action.payload,
        error: "",
      };
    case FETCH_USERS_FAILURE:
      return {
        loading: false,
        users: [],
        error: action.payload,
      };
    default:
      return state;
  }
};

const fetchUsers = () => {
  return function (dispatch) {
    dispatch(fetchUsersRequest());
    axios
      .get("https://jsonplaceholder.typicode.com/users")
      .then((response) => {
        // response.data is the users
        const users = response.data.map((user) => user.id);
        dispatch(fetchUsersSuccess(users));
      })
      .catch((error) => {
        // error.message is the error message
        dispatch(fetchUsersFailure(error.message));
      });
  };
};

// Store

const store = createStore(reducer, applyMiddleware(thunkMiddleware));

// Subscribe

const unsubscribe = store.subscribe(() => console.log(store.getState()));

// Dispatch
store.dispatch(fetchUsers());

```

# Redux Toolkit

Redux toolkit is the official, opinionated, batteries include tool set for efficient Redux development.

### Install Redux Toolkit

```bash
npm install @reduxjs/toolkit
```
