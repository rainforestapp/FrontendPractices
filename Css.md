# CSS

## CSS Modules
All new CSS should be written in CSS modules. Read more about them [here](https://github.com/css-modules/css-modules)

### Naming
Use camelCase for class names as per the [recommendation](https://github.com/css-modules/css-modules#naming) of the docs. It makes for better interoperability in js eg. you can refer to classes with `styles.tooltipContent` vs `styles['tooltip-content']`


### Customizing UI Components

In the past we've typically done this to customize css:

```jsx
<div className={classnames(props.className, styles.className)}></div>
```

The problem with that is it can result in undefined behavior - when you have to class names applied and they have conflicting properties, the one defined later in the resulting css wins.

Instead, use only the props class name, and fallback to a default builtin:

```jsx
<div className={props.className || styles.className}></div>

// or

const comp = ({ className = styles.className }) => <div className={className} />
```

In this case, if you only want to override one property of styles.className, then the css class where props.className is defined needs to import and compose what it needs from styles.className, and inside styles you can split out the more general styles:

```css
/* styles.css: */

.classNameBase {
  /* basic styles used for all instances */
}

.className {
  composes: classNameBase;
  /* more specific rules */
}

/* some parent component.css: */

@value classNameBase from '../some/component';
.thisComponent {
  composes: classNameBase;
  /* different specific rules here */
}
```

This means the composing is done in the actual css, and the rendered element only needs to know about one class name. However it's still ok to apply some conditional classes as needed, as long as they don't conflict with the main class:

```jsx
<div className={classnames(props.className || styles.className},
{
  [styles.editing]: isEditing,
  [styles.notEditing]: !isEditing,
}
)></div>
```

This approach should be used when the component being customized needs many changes outside of the scope of style props mentioned in the next section - whenever possible, use style props instead.

### Style Props

For common customizations such as margin or color, it can be quite painful to create CSS base classes and compose them for every instance of a UI element. Things like positioning should be easily modifiable by any component that wants to use some UI component. So in general, reusable/UI components should take in props for margin, color, size, and whatever is likely to be overridden, and then use propsToClassname to convert them into classes. The string passed in as the prop is given the proper prefix based on our CSS helper classes (`color="blue"` becomes the class `color-blue`). This way, you can simply do this:

```jsx
const SomeUiComponent = ({
  color = 'blue',
  size = 'small',
  margin = 'small',
  className = styles.className
}) => <div
  className={classnames(
    className,
    propsToClassnames({ color, size })
  )}
/>
```

```jsx
// render component with custom styles
<SomeUiComponent size="large" color="red" />
```

## Do Not Use `!important`

<https://css-tricks.com/when-using-important-is-the-right-choice/>

TLDR they fuck with specificity and worse, it’s next to impossible for anyone but the original writer to understand the intent of the importanted rule. This makes it very hard to fix and they end up needing to add their own !important to fight it. You are literally making everyone else write worse code.

## Avoid `float`

<http://www.sitepoint.com/give-floats-the-flick-in-css-layouts/> prefer to use [flexboxes](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)

--

*__Note: The convention outlined below may not make sense when using CSS modules and is only included for legacy reasons__*

## Only Use Class and Pseudo Selectors

This is actually part of the next rule

## Use BEM

read about BEM methodology:

- <http://getbem.com/introduction/>
-  <https://en.bem.info/methodology/key-concepts>

### Emphasis/Clarification

#### Positioning Blocks

Blocks should not have rules on positioning themselves since they are meant to be reusable. If you find yourself needing to position a block, then it is actually a [mix](https://en.bem.info/methodology/key-concepts/#mix) of a block and an element of it's parent.

#### Elements Cannot be Nested Under Another Element

taken from <http://getbem.com/faq/#css-nested-elements>

>What should I do if my block has a complex structure and its elements are nested? CSS classes like block__elem1__elem2__elem3 look scary.

According to BEM method, block structure should be flattened; you do not need to reflect nested DOM structure of the block. So, the class names for this case would be:

```css
.block {}
.block__elem1 {}
.block__elem2 {}
.block__elem3 {}
```

Whereas the DOM representation of the block may be nested:

```html
<div class='block'>
    <div class='block__elem1'>
        <div class='block__elem2'>
            <div class='block__elem3'></div>
        </div>
    </div>
</div>
```

Besides the fact that the classes look much nicer, it makes the elements be dependent on the block only. So, you can easily move them across the block when providing changes to the interface. The changes of the block DOM structure would not need corresponding changes to the CSS code.

```html
<div class='block'>
    <div class='block__elem1'>
        <div class='block__elem2'></div>
    </div>
    <div class='block__elem3'></div>
</div>
```
