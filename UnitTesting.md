# Unit Testing

Currently we use [jest](https://facebook.github.io/jest/) as our unit testing framework.

When building applications with React & Redux you will spend a fair amount of your time writing unit tests. These usually consist of actions, a component and a reducer.

## Folder Structure
As said in our [Folder Structure](FolderStructure.md) doc, we put our tests in a folder called `__tests__`, in the same directory as the module we are testing.

## Jest
When using jest, don't forget to call `jest.unmock('../myModule')` with the path of the module you are testing, since we have automocking turned on.

## Testing Actions
Since redux actions are simple functions, it's very easy to test them. 

### Testing simple synchronous actions
Given a simple action like this:

```js
// action.js
import { ADD_TEXT } from 'app/constants';

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
jest.unmock('../actions');
import { ADD_TEXT } from 'app/constants'; // we have our constants unmocked by default, hence we don't need to manually unmock them here
import { addText } from '../actions';

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
import { FETCHED_ITEMS, API_URL_ITEMS} from 'app/constants';
import { http } from 'app/services/http';

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
jest.unmock('../actions');
import { http } from 'app/services/http';
import { FETCHED_ITEMS, API_URL_ITEMS } from 'app/constants';
const { fetchItems } = require('../actions');

describe('text actions', () => {
  describe('addText', () => {
    it('returns text and right action type', () => {
      http.get.mockReturnValue(Promise.resolve('response payload')); // http.get is a mock function, ensure that it returns a promise
      const action = fetchItems();
      
      expect(action.type).toEqual(FETCHED_ITEMS);
      expect(http.get).toHaveBeenCalledWith(API_URL_ITEMS);

      let payload;
      action.payload.then((_payload) => { payload = _payload; });
      jest.runAllTimers();
      expect(payload).toEqual('response payload');
    });
  });
});
```

### Testing actions that use redux-thunk
To test actions that make use of the redux-thunk middleware, you really need an actual store, for this [redux-mock-store](https://github.com/arnaudbenard/redux-mock-store) is recommended.

Given an action that dispatches two http requests and two action types:

```js
// items.js
import { CREATED_ITEMS, FETCHED_ITEMS, API_URL_ITEMS } from 'app/constants';
import { http } from 'app/services/http';

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
// __tests__/actions.js
import configureMockStore from 'redux-mock-store'
import thunk from 'redux-thunk';
import { http } from 'app/services/http';
import { duplicateItems } from '../actions';
import { CREATE_ITEMS, FETCH_ITEMS, API_URL_ITEMS } from 'app/constants';

const middlewares = [ thunk ];
const mockStore = configureMockStore(middlewares); // create a mock store with the thunk middleware

describe('items actions', () => {
  let store;
  const items = ['item1', 'item2'];
  const itemsResponse = ['item3', 'item4'];
  describe('duplicateItems', () => {
    beforeEach(() => {
      store = mockStore({ });
      http.put.mockReturnValue(Promise.resolve());
      http.put.mockReturnValue(Promise.resolve(itemsResponse));
      store.dispatch(duplicateItems(items));
      jest.runAllTimers();
    });

    it(`fires PUT and dispatches ${CREATED_ITEMS} action`, () => {
      expect(http.put).toBeCalledWith(API_URL_ITEMS, { items });
      expect(store.getActions()).toContainEqual({ type: CREATED_ITEMS });
    });

    it(`re-fetches items and dispatches ${FETCHED_ITEMS} action`, () => {
      expect(http.get).toBeCalledWith(API_URL_ITEMS);

      jest.runAllTimers();

      expect(store.getActions()).toContainEqual({type: FETCHED_ITEMS, payload: items});
    });
  });
});
```


### Using the createMockStore Test Helper

We have a `createMockStore` helper that takes care of applying the middleware, so the above example can be written as:

```js
// __tests__/actions.js
import createMockStore from 'app/testHelpers/createMockStore';
import { http } from 'app/services/http';
import { duplicateItems } from '../actions'
import { CREATE_ITEMS, FETCH_ITEMS, API_URL_ITEMS } from 'app/constants';

describe('items actions', () => {
  let store;
  const items = ['item1', 'item2'];
  const itemsResponse = ['item3', 'item4'];
  describe('duplicateItems', () => {
    beforeEach(() => {
      store = createMockStore({ });
      http.put.mockReturnValue(Promise.resolve());
      http.put.mockReturnValue(Promise.resolve(itemsResponse));
      store.dispatch(duplicateItems(items));
      jest.runAllTimers();
    });

    it(`fires PUT and dispatches ${CREATED_ITEMS} action`, () => {
      expect(http.put).toBeCalledWith(API_URL_ITEMS, { items });
      expect(store.getActions()).toContainEqual({ type: CREATED_ITEMS });
    });

    it(`re-fetches items and dispatches ${FETCHED_ITEMS} action`, () => {
      expect(http.get).toBeCalledWith(API_URL_ITEMS);

      jest.runAllTimers();

      expect(store.getActions()).toContainEqual({type: FETCHED_ITEMS, payload: items});
    });
  });
});
```

## Testing a Component

We use [enzyme](https://github.com/airbnb/enzyme) for testing React components, it makes life a lot less painfull.

### Shallow vs Mount

We like the idea of shallow rendering because it constraints ourselves to testing a component as a unit, and ensures that we are not asserting the behaviour of child components. However, there are 2 very common pattern that severely diminishes the value of shallow rendering:

#### Internal subcomponents are not rendered

If in your module you decide to split your component into subcomponents, be it for reusability or organization, they won't be rendered.
```js
// Child.js
export default function Child() {
  return (<div className="child">
    {"Pretend this is a complex component that we don't want to test"}
  </div>);
}
// component.js
import Child from './Child';
const Foo = () => <div className="foo">This is foo!</div>;
const Bar = () => <div className="bar">This is bar!</div>;

export default function FooBar() {
  return (
    <div>
      <Foo />
      <Bar />
      <Child />
    </div>
  )
}

// __tests__/component.js
import FooBar from '../component';

const wrapper = shallow(<FooBar />);
expect(wrapper.find('.foo').exists()).toBeTruthy() // fails
```

#### It's awkward to test components enhanced with a high order component

This is inherently the same issue as the above
```js
// component.js
import { connect } from 'react-redux';
import compose from 'recompose/compose';
import withState from 'recompose/withState';
const Comp = ({show, setShow}) => (
  <div>
    {show && <span>shown!</span>}
    <button onClick={() => setShow(_show => !_show)}>Toggle</button>
  </div>
);

export default compose(
  connect(...),
  withState('show', 'setShow', false)
)(Comp);

// __tests__/component.js
import Comp from '../component';

const wrapper = shallow(Comp);
wrapper.find('button').simulate('click'); // can't find button

// there's a .dive helper that can shallowly render the first non-dom component
// but you need to know how many hocs you need to dive through
wrapper.dive().dive().find('button').simulate('click');

// also doesn't work if the hoc is stateful
expect(wrapper.dive().dive().find('span').exists()).toBeTruthy() // fails 
```

#### Solution: mount with manual component mocks

For all exported components, we can define a simple manual mock for them:
```js
// __mocks__/Child.js
export default function Child() { return null; }
```
When used in conjunction with snapshot testing, this will show a meaningful display name (otherwise you will only see generic `<Component />`s in the snapshot) and what props the component we are testing passes to it.

```jsx
// component.js
import Child from './Child';
const Foo = () => <div className="foo">This is foo!</div>;
const Bar = () => <div className="bar">This is bar!</div>;

export default function FooBar() {
  return (
    <div>
      <Foo />
      <Bar />
      <Child foo="foo" bar="bar" />
    </div>
  )
}

// __tests__/component.js
import FooBar from '../component';

const wrapper = mount(<FooBar />);
expect(wrapper.find('.foo').exists()).toBeTruthy() // passes

expect(wrapper).toMatchSnapshot();

// __tests__/__snapshots__/component.js.snap
<FooBar>
  <Foo>
    <div 
      className="foo"
    >
      This is foo!
    </div>
  </Foo>
  <Bar>
    <div
      className="bar"
    >
      This is bar!
    </div>
  </Bar>
  <Child
    foo="foo"
    bar="bar"
  />
</FooBar>
```

This will also work nicely for enhanced components:
```js
// component.js
import { connect } from 'react-redux';
import compose from 'recompose/compose';
import withState from 'recompose/withState';
const Comp = ({show, setShow}) => (
  <div>
    {show && <span>shown!</span>}
    <button onClick={() => setShow(_show => !_show)}>Toggle</button>
  </div>
);

export default compose(
  connect(...),
  withState('show', 'setShow', false)
)(Comp);

// __tests__/component.js
import Comp from '../component';

const wrapper = mount(Comp);
wrapper.find('button').simulate('click');
expect(wrapper.find('span').exists()).toBeTruthy() 
```

### Testing a redux connected component

Consider the component
```js
// component.js
import React from 'react';
import { connect } from 'react-redux';
import compose from 'recompose/compose';
import lifecycle from 'recompose/lifecycle';
import { fetchItems } from './actions';

const enhance = compose(
  connect(({items}) => ({ items }), { fetchItems }),
  lifecycle({
    componentDidMount() {
      this.props.fetchItems();
    }
  })
);

const Comp = ({ items }) => {
  if (items.length < 1) {
    return <div>no items!</div>;
  }

  return <div>have {items.length} items</div>;
};

export default enhance(comp);
```

We want to test that this component:
1) dispatches the fetchItems action on mount
2) displays the no items message when `items` is empty, otherwise
3) displays the item count

We use a helper to mock actions:
```js
// __mocks__/actions.js
import mockActions from 'app/testHelpers/mockActions';
module.exports = mockActions(require.resolve('../actions'));
```

with this, all exported actions have a mock implementation like so:
```js
import { createAction } from 'redux-actions';
const actionName = createAction('actionName');
```

We can use the mock store helper like such:
```js
// __tests__/component.js
jest.unmock('../component');

import React from 'react';
import { mount } from 'enzyme';
import createMockStore, { storeShape } from 'app/testHelpers/createMockStore';
import { fetchItems } from '../actions';
import Component from '../component';

let wrapper, store;
describe('component', () => {
  beforeEach(() => {
    store = createMockStore({
      items: ['item1', 'item2'],
    });

    fetchItems.mockReturnValue({ type: 'fetch items' });
    wrapper = mount(
      <Component />, 
      { context: {store}, childContextTypes: { store: storeShape } }
    );
  });

  it('dispatches fetchItems', () => {
    expect(store.getActions()).toContainEqual(fetchItems()); // this works because we mocked the action
  });

  it('displays item count', () => {
    expect(wrapper.find('div').text()).toEqual('have 2 items');
  });

  describe('when no items', () => {
    beforeEach(() => {
      store.setState({ items: [] });
    });

    it('displays no item message', () => {
      expect(wrapper.find('div').text()).toEqual('no items!');
    });
  });
});
```

## Testing a Reducer

...
