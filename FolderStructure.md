# FolderStructure

## Component Folder Structure

Our components live in `src/components`

Our frontend architecture is component based and that reflects our folder structure. Every component is bundled with it's actions, reducer, schema, tests and mocks. 

They all have the same layout:

```
SomeComponent
  index.js
  actions.js
  reducer.js
  schema.js
  __tests__
    index.js
    action.js
    reducer.js
    mockData.js
  __mocks__
    index.js
    ...
```

__Bear in mind__: Each of these files are optional. A component mostly only needs a few.

### index.js
Contains the React Component itself.

### actions.js
Contains all the component's actions.

### reducer.js
Contains the component's reducer.

### schema.js
Contains the [normalizr](https://github.com/gaearon/normalizr) Schema of our component.

### __tests__
Contains unit tests for component, actions and reducer.

### __mocks__
Contains manual mocks of any of the modules in the folder above (should be avoided). When there is a mock with an equivalent name, jest will use this instead of automatically mocking the module.

### mockData.js
For our unit tests we often need mock data. We put this in a separate file so it can be used by other modules too.

## Nested Components
If you are very certain that a component will only be used inside another (as a child component) and will not be shared, we nest the folder inside another. Like this:

```
SomeComponent
  index.js
  actions.js
  ...
  SomeChildComponent
    index.js
    actions.js
    ...
```
