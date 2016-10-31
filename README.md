# React Redux Basic Reducer

## Objectives

1. Write a reducer function
2. Understand the role reducers play in our stores

## Overview

There's one thing about our `createStore` method so far that's still kind of ugly - our `dispatch` method is hard-coded to figure out how to deal with different types of actions. Let's abstract that logic into a new function so that our `createStore` function can be re-usable. 

## Reducer

Let's take another look at our store's dispatch method.

```javascript
function createStore(){
  ...

  const dispatch = (action) => {
   switch(action.type) {
     case 'INCREMENT_COUNT':
      state++;
     case 'DECREMENT_COUNT':
      state--;
   }

   listeners.forEach( listener => listener(); ) 
  };

  ...

  return {
    getState: getState,   
    dispatch: dispatch,
    subscribe: subscribe     
    }
  }
}
```

We have a switch statement that looks at the type of action passed into dispatch and then updates the state accordingly. Let's move that into a new function. The function will take in the current state and an action and figure out what the new state should look like. In Redux, this type of function is called a 'reducer' because it's the type of thing you might pass into the array's `reduce` method. One rule about reducers in Redux: **the reducer must be pure functions**. This means they must not mutate the state or have any other side effects. We'll take a much deeper look at reducers later, but for now, just know that it must be a pure function. 

Let's create our counter reducer now. 

```javascript
function counterReducer(state, action){
  switch(action.type) {
    case 'INCREMENT_COUNT':
      return state + 1;
    case 'DECREMENT_COUNT':
      return state - 1;
   }
}
```

Note that **we're not actually mutating the state value that gets passed in** - instead, we're returning the new value based on the action type. 

Now, let's teach our dispatch method to use a custom reducer function to figure out how to update the state. 

```javascript
function createStore(reducer){
  ...

  const dispatch = (action) => {
   state = reducer(state, action);
   listeners.forEach( listener => listener(); ) 
  };

  ...

  return {
    getState: getState,   
    dispatch: dispatch,
    subscribe: subscribe     
    }
  }
}

const store = createStore(counterReducer);
```

Awesome, we can now create different stores with different reducers to figure out different copies of state. Two problems that we need to fix here.

1. When we first create our store, our `state` will be undefined. It would be nice for our `state` to start our with some default value. 
2. If we dispatch an action that our reducer doesn't know how to respond to, our function won't return anything.

We can fix both of these problems by giving our state a default value in our reducer function and returning it as the default for our switch statement.

```javascript
function counterReducer(state=0, action){
  switch(action.type) {
    case 'INCREMENT_COUNT':
      return state + 1;
    case 'DECREMENT_COUNT':
      return state - 1;
    default: 
      return state;
   }
}
```

Now, by default, we'll set our state to be some default value - since this is a simple counter, our state will be 0. A more complex piece of state, such as a list of tweets, might default to an empty array or an empty object. 

Finally, let's make sure that our store's initial state is our reducer's default value by dispatching an empty action in our `createStore` function.

```javascript
function createStore(reducer){
  ...

  const dispatch = (action) => {
   state = reducer(state, action);
   listeners.forEach( listener => listener(); ) 
  };

  dispatch({});

  return {
    getState: getState,   
    dispatch: dispatch,
    subscribe: subscribe     
    }
  }
}

const store = createStore(counterReducer);
store.getState() // 0
store.dispatch({type: 'INCREMENT_COUNT'})
store.getState() // 1
```

## Conclusion

Let's recap everything we've done so far.

1. We moved the responsibility of figuring out how to update state into a new function that we call a `reducer`. Our reducers are always pure functions - they return copies of state without actually mutating the state.
2. We made our `createStore` function more abstract by allowing us to pass in the reducer. Our `dispatch` method now depends on the reducer to determine how to update state.
3. Whenever we create a new store, we dispatch an empty action to make sure that our state starts off with the default value. 


## Resources

+ [Redux Docs on Reducers](http://redux.js.org/docs/basics/Reducers.html)
