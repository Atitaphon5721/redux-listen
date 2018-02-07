# redux-listen

[![Build Status](https://img.shields.io/travis/heiskr/redux-listen.svg?style=flat)](https://travis-ci.org/heiskr/redux-listen)

Use the listener pattern with Redux middleware.

-----

## reduxListenMiddleware

To add the middleware to your store:

```javascript
const reduxListenMiddleware = require('redux-listen').reduxListenMiddleware
const store = createStore(reducer, applyMiddleware(reduxListenMiddleware))
```

## addListener

To add a listener:

```javascript
addListener(SET_VAR, ({ action, state, dispatch }) => {
  ...
})
```

Now, whenever the action with the type `SET_VAR` dispatches, the middleware will call the function.

```javascript
addListener(/^FAIL_.*$/, ({ action, state, dispatch }) => {
  ...
})
```

You can also listen for actions where the action type matches a RegExp.

## addListeners

To add many listeners:

```javascript
// Or add many listeners
addListeners({
  [SET_VAR]({ action, state, dispatch }) {
    ...
  }
})
```

The advantage of adding using the "many" syntax is you get named functions for free.
Also in this case, whenever the action with the type `SET_VAR` dispatches, the middleware will call the function.

```javascript
addListeners({
  [FETCH_USERS]({ dispatch }, done) {
    fetchUser({ id: '1' }).then(() => {
      dispatch({ type: FETCH_USERS_SUCCESS })
      dispatch({ type: FETCH_NOTICES })
      done()
    })
  },

  [FETCH_NOTICES]({ state, dispatch }, done) {
    fetchNotices({ userToken: state.userToken }).then(() => {
      dispatch({ type: FETCH_NOTICES })
      done()
    })
  },
})
```

To chain network requests: dispatch an action when the first call is done, then listen for what you've dispatched.
You can also condition your chaining based on action or state properties.

## removeListeners

There's four ways to use `removeListeners`.

```javascript
removeListeners()
```

With no arguments, the middleware removes all listeners.

```javascript
removeListeners({ type: 'SET_VAR' })
```

With `type`, the middleware removes all listeners with the matching type.

```javascript
removeListeners({ fn: listenerFn })
```

With `fn`, the middleware removes all listeners with the same callback function.

You can also use both `type` and `fn` to remove listeners that match BOTH -- but not only `type` or only `fn`.

## onResolve

Got some async going on, and need to know when you're done "asyncing"?

```javascript
addListener(SET_VAR, ({ action, state, dispatch }, done) => {
  myPromise.then(() => {
    done()
  })
})

onResolve(function({ state, dispatch }) {
  alert('We are done asyncing! Page ready!')
})

dispatch({ type: 'SET_VAR' })
```

So two things here, there's a second real argument to the callback of `addListener`: `done`. If you ask for `done`, that means you have something async going on in that listener. Call `done` when that callback is totally finished.

When all the asyncs have finished, every function you've provided to `onResolve` up to that point we'll call. After that, the `onResolve` functions clear out. So if you need to do it again, you need to `onResolve` again as well.

## isPending

```javascript
isPending()
```

You'll get a true if you still have something asyncing, and false if the middleware isn't waiting on anything.

```
redux-listen
Copyright 2018 Kevin Heis and [contributors](https://github.com/heiskr/redux-listen/graphs/contributors)

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

  http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
