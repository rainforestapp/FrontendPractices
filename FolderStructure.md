# FolderStructure

## Component Folder Structure

Our components live in `src/components`

Our frontend architecture is component based and that reflects our folder structure. Every component is bundled with its actions, reducer, schema, tests and mocks.

Each file for the component, actions, reducer, spec etc. shoud have a unique file name for easy searching. The mocks, however, must match exactly in filenames and export patterns.

Example component:

```
SomeComponent
  index.js
  SomeComponent.js
  SomeSubComponent.js
  SomeComponent.css
  SomeComponentActions.js
  SomeComponentReducer.js
  SomeComponentSchema.js
  __tests__
    SomeComponent.spec.js
    SomeSubComponent.spec.js
    SomeComponentActions.spec.js
    SomeComponentReducer.spec.js
    mockData.js
  __mocks__
    index.js
    SomeComponent.js
    ...
```

__Bear in mind__: Some of these files are optional. A component mostly only needs a few.

### index.js
(Required) Exports all the components needed externally.

### SomeComponent.js
Contains the main component to be exported. Should be split into smaller sub-components when necessary.

### SomeComponentActions.js
Contains all the component's actions.

### SomeComponentReducer.js
Contains the component's reducer.

### SomeComponentSchema.js
Contains the [normalizr](https://github.com/gaearon/normalizr) Schema of our component.

### __tests__
Contains unit tests for component, actions and reducer.

### __mocks__
Contains manual mocks of any of the modules in the folder above (should be avoided). When there is a mock with an equivalent name, jest will use this instead of automatically mocking the module.

If you are using index.js as the export location, the __mocks__ directory needs to mimic the structure of the components (i.e. have a separate mock file for each component, import into index.js and export from there).

You need to import the actual component (not the mock) and it needs to have an absolute path. This allows it to be properly unmocked. See https://github.com/rainforestapp/regenwald/pull/3656/files#diff-057fdbe378b9542340e553f6e8f90926

### mockData.js
For our unit tests we often need mock data. We put this in a separate file so it can be used by other modules too.
