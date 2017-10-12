# Redux Selectors

Selectors are functions that accept the redux state (and optionally some arguments) and return some data. *Selectors should be the only thing you use to read data from stores.* This allows us to DRY up reading data from the store. If we need to change the shape of our store, we don't need to worry about what components read from them. We just need to change our selector.

Selectors should be placed in a `selectors.js` file next to the reducers they read from. All selector functions should be prefixed with `get`, eg. `getTestFeatureId`, `getRunGroupEnvironment`, etc.

If your selectors do heavy computation, it's beneficial to use [Reselect](https://github.com/reactjs/reselect) to create a memoized selector. See the [Computing Derived Data](http://redux.js.org/docs/recipes/ComputingDerivedData.html) section of the redux guide.

Baring exceptional circumstances, your selectors should aim to be as specific as possible when reading data from the store. One way a selector can be implemented is to read up to where an entity is in the store, and then allowing the selector to accept additional paths within the entity.

Example:
```
const getTest = ({ tests }, id, path) => tests.getIn([id, ...path);

// get the feature id of a test
getTest(state, 42, ['feature_id']);

// get reroute_geo of a test
getTest(state, 42, ['reroute_geo']);
```
Rather than doing this, create different selectors for each case
```
const getTest = ({ tests }, id, path) => tests.getIn([id, ...path);

// get the feature id of a test
const getTestFeatureId = (state, testId) => getTest(state, testId, ['feature_id']);

// get reroute_geo of a test
const getTestRoute = (state, testId) => getTest(state, testId, ['reroute_geo']);
```

This allows us to easily see what attributes of an entity is used accross the app. It's also easier to refactor should the location of an attribute within an entity changes
