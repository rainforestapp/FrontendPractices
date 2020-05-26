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

## 2 - Hooks

Use [hooks](https://reactjs.org/docs/hooks-intro.html) whenever local state and/or handlers are needed.
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

For `useEffect` hooks, consider naming the effect function for further clarity

```jsx
const Counter = ({ initialCount = 0 }) => {

  const [count, updateCount] = useState(initialCount);

  const increment = useCallback(() => updateCount(count => count + 1), [updateCount]);
  const decrement = useCallback(() => updateCount(count => count - 1), [updateCount]);

  useEffect(function setDocumentTitle() {
    window.document.title = `The current count is ${count}`;
  }, [count]);

  return (
    <div>
      {count}
      <Button onClick={increment}>Inc</Button>
      <Button onClick={decrement}>Dec</Button>
    </div>
  );
}
```
