# React Components
React components are the building blocks of our UI. If you're not sure what a React component is, you should first read [up on it here](https://facebook.github.io/react/).

There are a few forms to define React Component and they all have their tradeoffs. You should choose which form fits your needs best. We like our React components as simple as possible, to avoid complexity, hence we aim to use the simplest forms first. Below is a list of different forms of defining React components, starting with the simplest.

## Flow Types
All components should by default be typed [with flow](https://flow.org/en/docs/react/).

## Forms

Default to always using controlled components, only use uncontrolled components when necessary.

The idea that the data you see should be exactly the data you stored/passed in is the cornerstone of React(/Flux/Redux) programming. It __ensures predictability__. Any deviations from this principle __reduce predictability__. Controlled components show exactly the data that you passed in. Uncontrolled components don’t necessarily show exactly the data that you passed in. __Uncontrolled components are less predictable than controlled components__.

further reading at [Uncontrolled Components are an anti-pattern](https://medium.com/@jedwards8/uncontrolled-components-are-an-anti-pattern-abbdd86fd39e#.rzr8uan2c)

## 1 - Stateless Components
A stateless component is literally nothing other than a function which gets passed a props object and returns react element. It doesn't have state, or any of the Component methods like `willComponentMount` or `willComponentReceiveProps`. Here's an example:

```jsx
const Bouncer = ({age}) => {
  if (age > 18) {
    return (<div>You're good, please <a href="/enter">come in</a></div>);
  }
  return (<div>Sorry dude, no underage drinking. Come back in a few years.</div>);
}
```
As you can see, this is much like the component expression, with the only difference being that you now have a function body in which you can add logic to your component.

To add flow types to stateless components, simply type the props argument explicitly:
```
type Props = {
  age: number;
}

const Bouncer = ({ age }: Props) => (...);
```

## 2 - Component classes

Use component classes whenever local state and event handlers are needed. ES7 reduces a lot of boiler plate to use React Classes compared to recompose or other alternatives. 

A simple example of the usage would be:
```jsx
class Counter extends React.Component {
  static defaultProps = { initialCount: 0 }; 
  state = { count: this.props.initialCount }; 

  increment = () => this.setState(({ count }) => ({ count: count + 1 }));
  decrement = () => this.setState(({ count }) => ({ count: count - 1 }));

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

## 3 - Hooks

As an alternative to using class components, you can use hooks to add local state and handlers.

The same counter component can be implemented using hooks:
```jsx
const Counter = ({ initialCount = 0 }) => {

  const [count, updateCount] = useState(initialCount);

  const increment = useCallback(() => updateCount(count => count + 1), [updateCount]);
  const decrement = useCallback(() => updateCount(count => count - 1), [updateCount]);

  return (
    <div>
      {count}
      <Button onClick={increment}>Inc</Button>
      <Button onClick={decrement}>Dec</Button>
    </div>
  );
}
```

The section below is kept for historical reasons. After using hocs extensively for ~2 years we have decided to recommend avoiding it whenever possible.
If you're wondering why, the [official documentation](https://reactjs.org/docs/hooks-intro.html#motivation) has a section that explains it well. 
--

## 4 - HOCs

Use HOCs when React Classes / Stateless components are not enough or too restrictive to implement certain behaviours. A good rule of thumb is to not implement new HOCs in components and rather re-use existing ones. Some valid use-cases are

- Making sure state is persistent across user session. See [WithPersistentState](https://github.com/rainforestapp/regenwald/tree/develop/src/app/utilities/withPersistentState)
- Subscribing / transforming data from redux. See [connect](http://redux.js.org/docs/basics/UsageWithReact.html)
- Subscribing to action state changes. See [withButtonState](https://github.com/rainforestapp/regenwald/tree/develop/src/app/components/Button/withButtonState.js)
- Accessing / Interacting with React Router. See [withRouter](https://github.com/ReactTraining/react-router/blob/master/packages/react-router/docs/api/withRouter.md)

Following HOCs are discouraged to use independently and should only be used in conjunction with encouraged HOCs:
- [featureFlag](https://github.com/rainforestapp/regenwald/tree/develop/src/app/utilities/featureFlag). Use [FeatureFlag](https://github.com/rainforestapp/regenwald/blob/develop/src/app/v2/components/FeatureFlag/index.js) component instead
- withProps / mapProps / branch. Try to move those computations in the render part of the component itself.
- withState / withStateHandlers / withHandlers / lifecycle. Try using a React Class instead
