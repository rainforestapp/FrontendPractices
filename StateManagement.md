# State Management

There are several methods to store application state, depending on how long the state should be persisted, some methods are more suitable than others.

## Component Local State
TODO

## URL Query Strings
TODO

## Reducers ( / DB)
TODO

## Local Storage

Use [local storage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) to store state that should persist accross user sessions, but not important enough to store in the db (eg. page sizes when showing paginated lists). Local storage is not reliable, since [it's not always available](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API/Using_the_Web_Storage_API#Feature-detecting_localStorage) and can be cleared out by users / extensions for various reasons. We should treat local storage usage as progressive enhancement, meaning the app should still work when local storage is not available on the user’s browser for whatever reason and should behave sensibly.

Because of this limitation, local storage should only be used to store default values when initializing components. After a component is initialized, it may continue to update the local storage state, but it should not read / be controlled directly by the local storage state.
