# Naming Conventions

- The default for JavaScript variables is camelCase, starting with lower case. Examples: __aStandardVariableLikeThis__, __theApp__, __buttonMiddleware__.
- For classes (this includes react components), we capitalise the first letter. Examples: __BaseClass__, __ExampleReactComponent__, __FeaturedArticle__.
- For event listeners prefix your variables with __on__. Like __onClicked__, __onPress__, __onDidSomething__.
- Constants are capital case, separated by underscores. Use constants for variables that are shared among modules in your app, are immutable and only exist once. Examples are `STANDARD_TIMEOUT`, `API_URL`, `USER_DID_SOMETHING`. The third example here is an example of a redux action constant. Read more about [this here](http://redux.js.org/docs/basics/Actions.html).
- For variables containing booleans, prefix with __is__ or __has__. Examples: __isVisible__, __isActive__, __isDefault__, __hasSelectedElement__.
