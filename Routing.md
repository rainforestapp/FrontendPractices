# Routing

We use [react-router](https://github.com/reactjs/react-router) for routing in the frontend.

## Navigating Programatically

As mentioned in react-router's [docs](https://github.com/reactjs/react-router/blob/master/docs/guides/NavigatingOutsideOfComponents.md), you can navigate programatically by importing the history used by the app and calling it's methods. Only do this when you are outside of a component.

When using components, prefer navigating using the `router` prop that is made available by the `withRouter` hoc. This allows the component to work correctly regardless of which history is being used, and also improves modularity since everything you need will be provided in props. It also makes for easier testing since you can simply pass a mocked router object to the component.
