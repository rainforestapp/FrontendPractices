# Unit Testing

Currently we use [jest](https://github.com/facebook/jest/) as our unit testing framework, although this might change in the near future.

When building applications with React & Redux you will spend a fair amount of your time writing unit tests. These usually consist of actions, a component and a reducer.

## Folder Structure
As said in our [Folder Structure](FolderStructure.md) doc, we put our tests in a folder called `__tests__`, in the same directory as the module we are testing.

## Jest
When using jest, don't forget to call `jest.dontMock('../myModule')` with the path of the module you are testing, since Jest [automocks](https://facebook.github.io/jest/docs/automatic-mocking.html) all your modules to make real unit-testing easier.

Bear in mind that when you unmock modules, you need to import them by using `require` instead of `import` since babel's `import` statement does it's importing before jest does it's unmocking.

## Testing Actions
Since redux actions are simple functions, it's very easy to test them. 

### Testing simple synchronous actions
Given a simple action like this:

```js
// action.js
import { ADD_TEXT } from '../constants';

export function addText(text) {
  return {
    type: ADD_TEXT,
    text,
  };
}
```

This can be simply tested like so:

```js
// __tests__/actions.js
jest.dontMock('../actions');
import { ADD_TEXT } from '../../constants'; // we have our constants unmocked by default, hence we don't need to manually unmock them here
const { addText } = require('../actions');

describe('text actions', () => {
  describe('addText', () => {
    it('returns text and right action type', () => {
      expect(addText('foo bar')).toEqual(
        {
          type: ADD_TEXT,
          text: 'foo bar',
        }
      )
    });
  });
});
```

### Testing actions that return a promise
We implement a middleware which resolves actions that return promises. This is pretty handy.

Actions that return a promise are a tad more difficult to test. Here's an example which calls an endpoint

```js
// actions.js
import { FETCHED_ITEMS, API_URL_ITEMS} from '../constants';
import { http } from '../http';

export const fetchItems = () => (
  {
    type: FETCHED_ITEMS,
    payload: http.get(API_URL_ITEMS)
  }
);
```

Now when I test this, I want to know two things. Has http.get been called with the right url and has the right action type been dispatched. Hence I use a spy.

```js
// __tests__/actions.js
jest.dontMock('../actions');
import { http } from '../../http';
import { FETCHED_ITEMS, API_URL_ITEMS } from '../../constants';
const { fetchItems } = require('../actions');

describe('text actions', () => {
  describe('addText', () => {
    it('returns text and right action type', () => {
      spyOn(http, 'get');
      const action = fetchItems();
      expect(action.type).toEqual(type: FETCHED_ITEMS);
      expect(http.get).toHaveBeenCalledWith(API_URL_ITEMS);
    });
  });
});
```

### Testing actions that use redux-thunk
To test actions that make use of the redux-thunk middleware, you really need an actual store, for this [redux-mock-store](https://github.com/arnaudbenard/redux-mock-store) is recommended.

Given an action that dispatches two http requests and two action types:

```js
// items.js
import { CREATED_ITEMS, FETCHED_ITEMS, API_URL_ITEMS } from '../constants';
import { http } from '../../http';

export const duplicateItems = (items) => (dispatch) => (
  http.post(API_URL_ITEMS, {items})
  .then(() => {
    dispatch({type: CREATED_ITEMS })
    return http.get(API_URL_ITEMS)
  })
  .then((items) => (dispatch({type: FETCHED_ITEMS, payload: items})))
);
```

What we want to verify here is which urls and payloads the http service has been called with, along with what actions and payloads have been dispatched.

```js
// __tests__/actions.j
import configureMockStore from 'redux-mock-store'
import thunk from 'redux-thunk'
impprt { http } from '../../http';
import { duplicateItems } from '../actions'
import { CREATE_ITEMS, FETCH_ITEMS } from '../../constants';

const middlewares = [ thunk ]
const mockStore = configureMockStore(middlewares)

describe('items actions', () => {
  describe('duplicateItems', () => {
    const store = mockStore({ items: [] });
    store.dispatch(duplicateItems());
  });
});
```


## Testing a Component

We use [enzyme](https://github.com/airbnb/enzyme) for testing React components, it makes life a lot less painfull.


## Testing a Reducer

...
