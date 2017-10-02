# Redux Selectors

Selectors are functions that accept the redux state (and optionally some arguments) and return some data. *Selectors should be the only thing you use to read data from stores.* This allows us to DRY up reading data from the store. If we need to change the shape of our store, we don't need to worry about what components read from them. We just need to change our selector.

Selectors should be placed in a `selectors.js` file next to the reducers they read from.

If your selectors do heavy computation, it's beneficial to use [Reselect](https://github.com/reactjs/reselect) to create a memoized selector. See the [Computing Derived Data](http://redux.js.org/docs/recipes/ComputingDerivedData.html) section of the redux guide.