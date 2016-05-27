# CSS

## Do Not Use `!important`

<https://css-tricks.com/when-using-important-is-the-right-choice/>

TLDR they fuck with specificity and worse, it’s next to impossible for anyone but the original writer to understand the intent of the importanted rule. This makes it very hard to fix and they end up needing to add their own !important to fight it. You are literally making everyone else write worse code.

## Avoid `float`

<http://www.sitepoint.com/give-floats-the-flick-in-css-layouts/>

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
