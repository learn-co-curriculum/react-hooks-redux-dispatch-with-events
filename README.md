# Completing our Counter Application

## Objectives

In this lesson, you will learn the following:

* How to allow a user to execute the dispatch function by attaching dispatch to
  event listeners.
* The redux flow.

Use `js/reducer.js` to follow along. The file is already set up in `index.html`,
so if you run `open index.html`, any code in `js/reducer.js` will execute.

## Application Goal

We have built out most of the redux pattern.  Don't worry, we'll review it.

For now, let's talk about what we want as a user experience.  Here it is: you
click on a button, and you see a number on the page go from zero to one.  Click
again, and you see the number go from one to two.  We can see a couple of steps
involved in this.

1. Clicking on the button should change the state.  
2. This change in state should be rendered.

## Brief Redux Review

By now, you've learned a lot about redux, but the basic story about it has not
changed:

`Action -> Reducer -> New State`

For example, to increase our state we call `dispatch({type: 'INCREASE_COUNT'})`.
Our dispatch function calls our reducer which updates state, and then we render
that view on the page.

In the previous section, we learned that by dispatching an initial action and
having a default argument in our reducer, we can set up our initial state.

## Rebuild our Dispatch Function and our Reducer

Let's code out this our counter application from scratch.  

#### 1. Start by remembering our core fact about how redux works.

`action -> reducer -> new state`

Ok, let's translate that into code.  This means if we pass an action and a
previous state to our reducer, the reducer should return the new state.

```javascript
let state = {count: 0}

function reducer(state, action){
  switch (action.type) {
    case 'INCREASE_COUNT':
      return {count: state.count + 1};
    default:
      return state;
  }
}
```

Ok copy this into the `reducer.js` file inside the `js` folder.  Now let's get
some feedback that we did this correctly by opening up our `index.html` file in
chrome.  From your terminal type open `index.html`.  Now this index file has a
link to the `reducer.js` file, so your code will be accessible from the console
- press command+shift+c to open it up.  Now let's test the code by calling the
  `reducer()` function:

```javascript
reducer({count: 0}, {type: 'INCREASE_COUNT'});
```

If you see a return value of `{count: 1}` then give yourself a big smile. :)

Ok, if we type in state, we see that it's unchanged.  We need to assign our
state to be the return value of our reducer each time that we call the reducer.
So how do we do that?  Think hard, there's no rush.

Thinking...

Thinking...

#### 2. Wrap the execution of our reducer in a function that we call dispatch

Ok, so we can reassign the state by adding the dispatch function to our
`reducer.js` file.  This dispatch function should receive an argument of action.
It can access the state because it is declared earlier in the file in global scope.  

```javascript
function dispatch(action){
  state = reducer(state, action);
}
```

Now let's see if this reassigns state. Add this `dispatch` function in and open
or refresh the `index.html` file in a browser tab. Call `dispatch({type:
'INCREASE_COUNT'})`.  It should return `undefined`, since `dispatch()` doesn't
return anything, but our `state` value should have changed! Type in `state` and
see if this is true.  State should return `{count: 1}`.  Hurray! More smiles. :) :)

Next problem.  Our state says the count is 1, but do you think that is
reflected in our HTML?  Me neither.  Ok, so what function is in charge of that.
Give it a shot.  I'll be waiting with the answer when you're ready.

#### 3. Use the render function to display our state.

Ok, so now we need a function called render that will place this count on the page.

```javascript
function render(){
  let container = document.getElementById('container');
  container.textContent = state.count;
}
```

So now when we call `render` from the console we should see HTML that reflects
the current count. Entering `dispatch({type: 'INCREASE_COUNT'})` to change
state, then `render` again should update the number displayed.

Since the two functions go together, the next step is to tie rendering with the
dispatch function. Easy enough. Let's alter our dispatch method so that it looks
like this:

```javascript
function dispatch(action){
  state = reducer(state, action);
  render();
}
```

Ok, so now each time we dispatch an action we should have to update our HTML
because the `render` function is also called.  Now for a little refactoring.
Let's have only our initial state set in the reducer.  We do that by setting our
initial state as a default argument to our `reducer` reducer.  Go ahead and
tackle it.  We'll show the code below.

#### 4. Use a default argument in the reducer to set the initial state.

Now our `reducer()` function should look like the following:

```javascript
// let state = {count: 0}
function reducer(state = {count: 0}, action){
  switch (action.type) {
    case 'INCREASE_COUNT':
      return {count: state.count + 1}
    default:
      return state;
  }
}
```

We are commenting out/deleting the top line where we assign the state, because
dispatching an action should take care of it (it doesn't).  Call `dispatch` with
an action like `dispatch({type: 'INCREASE_COUNT'})`, and we would hope that
because state is `undefined`, our default argument will be passed through.  The
problem is that we still need to declare our state.  So now our updated
(working) code looks like the following.

```javascript
let state;
function reducer(state = {count: 0}, action){
  switch (action.type) {
    case 'INCREASE_COUNT':
      return {count: state.count + 1}
    default:
      return state;
  }
}
```

Call `dispatch({type: 'INCREASE_COUNT'})` again, and we get no error.  Instead
we get a beautiful piece of HTML code that says the number 1 on it.  Now, if
instead we want to show the number zero, our default state, well we can just
refresh our page, and then dispatch an action that returns the default state
like so: `dispatch({type: '@@init'})`.  This does not increase our state, but it
does return our default state and then call render.

This is what we want to do each time we open our page.  So let's add
`dispatch({type: '@@INIT'})` at the end of our javascript file.  This is where
we left off previously.  Our almost completed code should look like the
following.

```javascript
let state;

function reducer(state = {count: 0}, action){
  switch (action.type) {
    case 'INCREASE_COUNT':
      return {count: state.count + 1}
    default:
      return state;
  }
}

function dispatch(action){
  state = reducer(state, action)
  render()
}

function render(){
  let container = document.getElementById('container');
  container.textContent = state.count;
}

dispatch({type: '@@INIT'})
```

Looks good.  But we're going further today.  We need to make sure every time the
user clicks on a button, we dispatch an action.  How do you think we do that.

#### 5. Integrating dispatch with a user event

So `dispatch` is responsible for updating the state and re-rendering.  And we
want an action to be dispatched each time a user clicks.  So let's attach
`dispatch` execution to a click event.

We'll be writing a "vanilla" JavaScript event listener.

```javascript
let button = document.getElementById('button');

button.addEventListener('click', () => {
  dispatch({type: 'INCREASE_COUNT'})
})
```

Now every time we click, we dispatch an action of type increase.  Dispatch first
calls our reducer, which updates our state.  Then dispatch renders the updated
view.

Putting everything together, our code should look like this:

```js
let state;
function reducer(state = {count: 0}, action){
  switch (action.type) {
    case 'INCREASE_COUNT':
      return {count: state.count + 1}
    default:
      return state;
  }
}

function dispatch(action){
  state = reducer(state, action);
  render();
}

function render(){
  let container = document.getElementById('container');
  container.textContent = state.count;
}

dispatch({type: '@@INIT'})

let button = document.getElementById('button');

button.addEventListener('click', () => {
  dispatch({type: 'INCREASE_COUNT'})
})
```

Click the button.  Our application is done!

## Summary

Oh yea!  Not much new here.  But that didn't stop the dopamine hit. We saw that
by thinking about redux from the perspective of `action -> reducer -> new
state`, we are able to get going.  Then it's just a matter of tackling each
problem.

As for new information, we saw that we can get the user to call the `dispatch`
method, by executing `dispatch` from inside the callback of an event handler.
