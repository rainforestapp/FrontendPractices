# React Components
React components are the building blocks of our UI. If you're not sure what a React component is, you should first read [up on it here](https://facebook.github.io/react/).

There are a few forms to define React Component and they all have their tradeoffs. You should choose which form fits your needs best. We like our React components as simple as possible, to avoid complexity, hence we aim to use the simplest forms first. Below is a list of different forms of defining React components, starting with the simplest.

## PropTypes
All component classes need to be decorated [with proptypes](https://facebook.github.io/react/docs/reusable-components.html).

All PropTypes should be __required__ by default, and only relaxed if needed.

## 1 - Component Expressions - for logic-less components
The most restrictive form React component is a fat arrow expression. Here's an example:

```jsx
const HelloComponent = ({ name }) => (<h1>{name}</h1>);
```

This is basically just an expression, not allowing for logic of any kind. It's the simplest, most restrictive form of component.

To add proptypes to component expressions, simply attach them to the variable:
```js
HelloComponent.propTypes = {
  name: PropTypes.string.isRequired,
};
```

## 2 - Stateless Components
A stateless component is literally nothing other than a function which gets passed a props object and returns react element. It doesn't have state, or any of the Component methods like `willComponentMount` or `willComponentReceiveProps`. Here's an example:

```jsx
const Bouncer = ({age}) => {
  if (age > 18) {
    return (<div>You're good, please <a href="/enter">come in</a></div>);
  }
  return (<div>Sorry dude, no underage drinking. Come back in a few years.</div>);
}
```
As you can see, this is much like the component expression, with the only difference beeing that you now have a function body in which you can add logic to your component.

To add propTypes to stateless components, simply attach them to the variable:
```
Bouncer.propTypes = {
  age: PropTypes.number.isRequired,
};
```

## 3 - Component classes

There are a bunch of reasons why you might need a compon

... to be written


React components have two containers for data: `state` and `props`.
