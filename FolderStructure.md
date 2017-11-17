# Folder Structure


This is the folder structure we are using:

```
src/app
  application.js
  Router.js
  state.js
  Components
    TestList
      index.js
      TestList.js
      __mocks__
        index.js
        TestList.js
      __tests__
        TestList.js
    Button
      index.js
      Button.js
      __mocks__
        index.js
        Button.js
      __tests__
        Button.js
  Views
    Tests
      index.js
      Tests.js
      __mocks__
        index.js
        Tests.js
      __tests__
        Tests.js
    Results
      index.js
      ...
      RunSummary
        index.js
        ...
        TestResult
          index.js
          ...
  Models
    Feature
      index.js
      FeatureReducer.js
      FeatureSelectors.js
      FeatureActions.js
      FeatureTypes.js
      FeatureSchema.js
      FeatureUtils.js
      __mocks__
        index.js
        FeatureActions.js
        ...
      __tests__
        FeatureReducer.js
        FeatureSelectors.js
        ...
```

`application.js` is the application entry point.
`Router.js` contains the application route configuration.
`state.js` contains the redux store.

Our frontend architecture is comprised of 3 main entities: `Components`, `Views`, and `Models`.

## Components

A place for reusable react components. To be useful they need to be imported and rendered by a view. Components defined at the root `Components` folder can be used anywhere in the application. Components nested in another component can only be used by it's direct parent.

## Views

These refer to actual pages in the application. They should be imported and rendered by the Router. Components can be defined within a view but it's should only be used within that view. The main purpose for defining sub components within a view should be for organization or reuse within that view. For more general reusability that component should be placed under the `Components` folder.

## Models

Each model represents a single concept of the application. This is heavily inspired by [re-ducks](https://github.com/alexnm/re-ducks). Most of the time this will be the frontend representation of a backend model (tests, runs, features, etc.) but they can also represent a concept that doesn't exist in the backend (eg. notifications).

Files in a model:
- Reducer
- Schema - [Normalizr](https://github.com/gaearon/normalizr) schema, api response payload should be normalized before being stored in the reducer.
- Selectors - Reading from the store should only be done through selectors.
- Actions - Contains all actions related to the model.
- Types - Contains both action type and flow type definitions.
- Utilities - Helper functions related to this particular model.

These files should be prefixed by the model name, so for a `Feature` model, the file names should be:
- `FeatureReducer.js`
- `FeatureSchema.js`
- `FeatureSelectors.js`
- `FeatureActions.js`
- `FeatureTypes.js`
- `FeatureUtils.js`

## Common Files

## index.js
Every Model, View, and Component should have an `index.js` file that serves as an entry point to that module.

### __tests__
Contains unit tests for that module.

### __mocks__
Contains manual mocks of any of the modules in the folder above (should be avoided). When there is a mock with an equivalent name, jest will use this instead of automatically mocking the module.

If you are using index.js as the export location, the __mocks__ directory needs to mimic the structure of the components (i.e. have a separate mock file for each component, import into index.js and export from there).

You need to import the actual component (not the mock) and it needs to have an absolute path. This allows it to be properly unmocked. See https://github.com/rainforestapp/regenwald/pull/3656/files#diff-057fdbe378b9542340e553f6e8f90926

### mockData.js
For our unit tests we often need mock data. We put this in a separate file so it can be used by other modules too.
