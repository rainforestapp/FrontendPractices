# Naming Conventions

### File suffixes
- Always use `.js` for javascript files, whether they contain jsx or not

### camelCase

The default for JavaScript variables is camelCase, starting with lower case.

Examples: __aStandardVariableLikeThis__, __theApp__, __buttonMiddleware__.

### Classes

For classes (this includes react components), we capitalise the first letter.

Examples: __BaseClass__, __ExampleReactComponent__, __FeaturedArticle__.

### Event Listeners

For event listeners prefix your variables with __on__. 

Examples: __onClicked__, __onPress__, __onDidSomething__.

### Constants

Constants are capital case, separated by underscores. Use constants for variables that are shared among modules in your app and should only exist once.

Examples: `STANDARD_TIMEOUT`, `API_URL`, `USER_DID_SOMETHING`. The third example here is an example of a redux action constant. Read more about [this here](http://redux.js.org/docs/basics/Actions.html).

### Booleans

For variables containing booleans, prefix with __is__ or __has__.

Examples: __isVisible__, __isActive__, __isDefault__, __hasSelectedElement__.

### Analytic events

Try to follow the [Object Action Framework](https://segment.com/academy/collecting-data/naming-conventions-for-clean-data/) when naming events and their properties.
