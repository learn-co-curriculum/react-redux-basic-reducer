# React Redux Basic Reducer

## Objectives

1. Write a reducer function
2. Understand the role reducers play in our stores

## Outline

1. Show how it's ugly for dispatch to be figuring out what the new copy of state should be. 
2. It also means that our `createStore` method can't really be reused
3. Extract that logic into a reducer
4. Explain that reducers should be pure - not manipulating state, always returning a fresh copy.
