---
layout: post
title:  Begin with Redux
category: redux 
description: I've started my programming journey with Redux today, here is my notebook.
---

# The Beggining

I will try to explain to myself as a total begginer, what I have done this day to know something more about **modern** web developing process.

## npm

As a start, it is needed to download npm, which is `Node.JS` repository, from where, I will probably download most of the tools.
To do that, simply go to `Node.JS` Page and download zip file and install it somewhere. Best way is to install on `C:/` directory on Windows
(Yes, for now I'll be using Windows because I'm too poor for having a Mac), because I couldn't find a nice way to navigate between partitions
in `cmd`. 

## Redux

I have decided to start on the Roots, so now I am checking **[Redux](http://redux.js.org/)** website. It should take some time before I will get any 
**stable** knowledge from this. 

I have installed needed things from `Read Me` section on this site.  

Also in `Read Me` there was a link to **[30 videos from Redux creator](https://egghead.io/lessons/javascript-redux-the-single-immutable-state-tree?course=getting-started-with-redux)** - Dan Abramov.

I will write some most important notes from each video here:

## 1 
      Everything that changes in application (data, UI state) is contained in a single object - `State Tree`.
    - All changes can be seen in the console.
## 2
    State tree is redundant. Only way to change the state tree is by dispatching an `action`.
    Only most required fields are beeing used by describing an `action`.
    For example - if you have filter showing specified array of Strings. Like Strings containing type of fruit, or color.
    The filter only shows an action what it does - `Set_Filter` and by what property it filters - `Show_Fruits`.
    An action doesn't need to know what it filters. But how should it filters.

## 3
   **Pure and Impure Functions** 
    - `Pure functions` - are those, which return value only depend on function arguments. When You pass the same arguments, you will get the same result every time. Also it will not modify values passed to function before.
    - `Impure functions` - These are the opposites of `Pure functions` - can return different results, can change arguments passed to them.
    Important thing from this is, that some functions in Redux need to be a `Pure functions`.

## 4
   **The Reducer**
    - To describe sate mutations, it is needed to write a function that takes as an argument `Previous state` of the app, an `Action beeing dispatched`, and returns `Next state`. This action needs to be pure.
## 5
   **Writing a Reducer**
    - Each state must be defined and function need to know where it is everytime, to know what to return.

```javascript
// counter.js
const counter = (state = 0, action) => {
    switch (action.type) {
        case 'INCREMENT':
            return state +1;
        case 'DECREMENT':
            return state -1;
        default:
            return state;
    }
}
```
## 6
   **Store**

The store binds together three principles of Redux.
    - holds current app state object.
    - let you dispatch `actions`.
    - You need to specify `Reducer` to change state updates with `actions`.

```javascript
// counter.js
const counter = (state = 0, action) => {
    switch (action.type) {
        case 'INCREMENT':
            return state +1;
        case 'DECREMENT':
            return state -1;
        default:
            return state;
    }
}

//creating a Store 
const { createStore } = Redux;
const store = createStore(counter);

//Retrieves current state of the Redux Store
console.log(store.getState()); //state = 0

//Lets dispatch actions that change the state of application
store.dispatch({type: 'INCREMENT' });
console.log(store.getState()); //state = 1

//Lets register a callback that Redux will call anytime
//actions has been dispatched
store.subscribe(() =>{
    document.body.innerText = store.getState();
});

//On each click on the screen the state will increment, initial is = 0
document.addEventListener('click', () =>{
    store.dispatch({ type: 'INCREMENT' });
});

```

## 7
  **Store Method from Scratch**

```javascript
// createStore.js
const createStore = (reducer) => {
    let state;
    let listeners = [];

    const getState = () => state;

    const dispatch = (action) => {
        //call reducer to calculate current state and action beeing dispatched
        state = reducer(state, action);
        //after state was updated, it is needed to notify 
        //every listener about that fact
        listeners.forEach(listener => listener ());
    };
    
    const subscribe = (listener) => {
        listeners.push(listener);
        //unsubscribing, removes listener from listeners array
        return () =>{
            listeners = listeners.filter(l => l !== listener);
        };
    };
    //after store is returned, populate initial state
    dispatch({});

    return { getState, dispatch, subscribe};
};
```
## 8
   **Counter example**

    Writing down an transcript for that video, many new things...

> In the simplest counter example, I update the document body manually any time this tool state changes. But, of course, this approach does not scale to complex applications. Instead of manually updating the DOM, I'm going to use React.
I'm adding two scripts corresponding to React and react-dom packages and a root `<div/>` to render to. Now I can call the `ReactDOM.render` with my `root` component. The `render` function is called any time this store state changes, so I can safely pass the current state of this store as a `prop` to my `root` component.

```javascript
const render = () => {
  ReactDOM.render(
    <Counter value={store.getState()} />
    document.getElementById('root')
  );
}
```

>Since this `state` is held inside the Redux Store, the `Counter` component can be a simple function, which is a supported way of declaring components since React 14.

```javascript
const Counter = ({value}) => (
  <h1>{value}</h1>
);
```

>I want to add, `DECREMENT`, and `INCREMENT` buttons to the component, but I don't want to hard-code the Redux dependency into the component. So I just add `onIncrement` and `onDecrement` props as callbacks. In my `render` method, I pass the callbacks that call `store.dispatch` with appropriate actions. Now the application state is updated when I click the buttons.

```javascript

const Counter = ({
  value,
  onIncrement,
  onDecrement
}) => (
  <div>
  <h1>{value}</h1>
  <button onClick={onIncrement}>+</button>
  <button onClick={onDecrement}>-</button>
  </div>
);

<Counter
      value={store.getState()}
      onIncrement={() =>
        store.dispatch({
          type: 'INCREMENT'           
        })            
      }
      onDecrement={() =>
        store.dispatch({
          type: 'DECREMENT'           
        })            
      }
    />
```

>Let's recap how this application works. The `counter component` is what I call a **dumb component**. It does not contain any **business logic**. It only specifies how the current application state transforms into renderable output and how the callbacks, passed via props, are bound to the event handlers.

>When we render a counter, we specify that its value should be taken from the Redux Store current state. When the user presses `INCREMENT` or `DECREMENT`, we dispatch corresponding action to the Redux Store. Our reducer specifies how the next state is calculated based on the current state and the action being dispatched.

>Finally, we `subscribe` to the Redux Store, so our `render` function runs anytime the state changes, so the counter gets the current state.

