General style guidelines

## Conditional Statements

Generally, traditional `if(...) {...} else {...}` statements are the preferred way of writing conditional logic.
Favour ternaries over if statements only if they are short easy to read one liners, __like this:__

```js
const bam = something ? 'something' : 'somethingElse';
```

If the conditional spans over multiple lines, you shouldn't use a ternary expression.
Here's an example including JSX: __Avoid this:__
```jsx
return (
  <div>
    {
      isSomething === somethingElse ?
      (
        <div>Bababoom
          <ul>
            <li>Boing 1</li>
            <li>Boing 2</li>
          </ul>
        </div> 
      ): null
    }
  </div>
);
```

Instead you can write something like 
```jsx
let boom = null;
if (isSomething === somethingElse) {
  boom = (<div>Bababoom
    <ul>
      <li>Boing 1</li>
      <li>Boing 2</li>
    </ul>
  </div>);
}
return <div>{boom}</div>;
```


Although seemingly convenient (because this way we can have conditional logic inside jsx) it makes code hard to read.

## Comma separated values and react props
Values and props are allowed on single lines if the line doesn't get too long:

A jsx example:
```jsx
<SomeComponent type="Boom" hey="Boing" items={items} yo="hello" />
```

A plain array and object:
```js
const array = ['d21', 'w1f432', 123];
const object = { a: 'd21', b: 'w1f432', c: 123 };
```

If the line gets too long please stack the values:

A jsx example:
```jsx
<SomeComponent
  type="Boom"
  hey="Boing"
  items={items}
  yo="hello"
/>
```

An example of an object:
```js
const object = {
  a: 'd21',
  b: 'w1f432',
  c: 123,
};
```

Things to avoid:

Never mix the two, creating a table-like organisation, this makes code much harder to read effectively:
Example: (__AVOID__)
```jsx
<SomeComponent
  type="Boom" hey="Boing" do={() => something()} key={index}
  items={items} hey="boing" yo="hello" place="left"
  onClick={() dispatchAnActionHereThatDoesAwesomeCrap()}
/>
  
```


- Never nest ternaries.
- Double-quotes for 'static' react propTypes like `<something some="hello"/>`.
- Backtick quotes for string concatenation, also in jsx: ```<something some=`${a}_${b}`/>```
