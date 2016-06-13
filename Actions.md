# Redux Actions

We currently employ different types of action creators, depending on the task at hand.

## 1. Action Shape

We use [redux-actions](https://github.com/acdlite/redux-actions) for enforcing an action shape throughout the app.

This is how it looks:

```js
{
  type: ACTION_CONSTANT,
  payload: Promise|Object|String|Integer,
}
```

## 2. Promises as payload

We use a promise middleware akin to @acdlite's [redux-promise](https://github.com/acdlite/redux-promise).
If an action contains a promise as payload, the promise middleware will automatically resolve the promise and dispatch the returned value.

Here's an exaxmple of an action creator returning a promise:

```js
import { createAction } from 'redux-actions';
import { http } from 'http';
import { FETCHED_ITEMS, ITEMS_URL } from '../../constants';

const fetchItems = createAction(
  FETCHED_ITEMS,
  () => (
    http.get(ITEMS_URL)
    .then((response) => (response.tests))
  )
);
```

## 3. Async Await

Async await also relies on the promise middleware.
If you use async await, the above action creator can also be defined as:

```js
const fetchItems = createAction(
  FETCHED_ITEMS,
  async () => (
    const { tests } = await http.get(ITEMS_URL);
    return tests;
  )
);
```

### 4. Thunks

We use [redux-thunk](https://github.com/gaearon/redux-thunk) in situations where we need something more powerful than the above approaches or when an action doesn't map to one action constant (for example if it dispatches multiple actions).

```js
const createItemAndDoSomething = () => (dispatch) => {
  dispatch(createItem());
  dispatch(doSomething());
}
```

### 5. Thunks with promises

Other times we want to use promises inside thunks to chain several actions together. Since our promise middleware returns the promise back, we can chain dispatches like so:

```js
const createItemAndThenDoSomething = () => (dispatch) => {
  dispatch(createItem())
  .then((response) => {
    dispatch(doSomethingWithItem(response.item));
  });
}
```
