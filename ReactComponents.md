# React Components
React components are the building blocks of our UI. If you're not sure what a React component is, you should first read [up on it here](https://facebook.github.io/react/).

There are a few forms to define React Component and they all have their tradeoffs. You should choose which form fits your needs best. We like our React components as simple as possible, to avoid complexity, hence we aim to use the simplest forms first. Below is a list of different forms of defining React components, starting with the simplest.

## Flow Types
All components should by default be typed [with flow](https://flow.org/en/docs/react/).

## Forms

Default to always using controlled components, only use uncontrolled components when necessary.

The idea that the data you see should be exactly the data you stored/passed in is the cornerstone of React(/Flux/Redux) programming. It __ensures predictability__. Any deviations from this principle __reduce predictability__. Controlled components show exactly the data that you passed in. Uncontrolled components don’t necessarily show exactly the data that you passed in. __Uncontrolled components are less predictable than controlled components__.

further reading at [Uncontrolled Components are an anti-pattern](https://medium.com/@jedwards8/uncontrolled-components-are-an-anti-pattern-abbdd86fd39e#.rzr8uan2c)

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

To add flow types to stateless components, simply type the props argument explicitly:
```
type Props = {
  age: number;
}

const Bouncer = ({ age }: Props) => (...);
```

## 3 - Component classes

Use component classes whenever local state and event handlers are needed. ES7 reduces a lot of boiler plate to use React Classes compared to recompose or other alternatives. 

A simple example of the usage would be:
```
class Counter extends React.Component {
  static defaultProps = { initialCount: 0 }; 
  state = { count: this.props.initialCount }; 

  increment = () => this.setState({ count: this.state.count + 1 });
  decrement = () => this.setState({ count: this.state.count - 1 });

  render() {
    return (
      <div>
        {this.state.count}
        <Button onClick={this.increment}>Inc</Button>
        <Button onClick={this.decrement}>Dec</Button>
      </div>
    );
  }
}
```

## 4 - HOCs

Use HOCs when React Classes / Stateless components are not enough or too restrictive to implement certain behaviours. A good set usecases would be

- Making sure state is persistant across user sesstion. See [WithPersistentState](https://github.com/rainforestapp/regenwald/tree/develop/src/app/utilities/withPersistentState)
- Subscribing / transforming data from redux. See [connect](http://redux.js.org/docs/basics/UsageWithReact.html)
- Subscribing to action state changes. See [withButtonState](https://github.com/rainforestapp/regenwald/tree/develop/src/app/components/Button/withButtonState.js)
- Accessing / Interacting with React Router. See [withRouter](https://github.com/ReactTraining/react-router/blob/master/packages/react-router/docs/api/withRouter.md)

Following HOCs are discouraged to use independently and should only be used in conjunction with encouraged HOCs:
- [featureFlag](https://github.com/rainforestapp/regenwald/tree/develop/src/app/utilities/featureFlag). Use [FeatureFlag](https://github.com/rainforestapp/regenwald/blob/develop/src/app/v2/components/FeatureFlag/index.js) component instead
- withProps / mapProps / branch. Try to move those computations in the render part of the component itself.
- withState / withStateHandlers / withHandlers / lifecycle. Try using a React Class instead
