# Components

## Templates

1. Templates are HTML.
2. In addition to normal HTML syntax, Ember allows you to use self-closing syntax `(<div />)` as a shorthand for an opening and closing tag `(<div></div>)`.

## Component Arguments

1. Components become useful building blocks of our app if we make them reusable.
1. Arguments syntax: `{{@argument}}`: `<Avatar @title="Tomster's avatar" @initial="T" />`
1. `...attributes` syntax determines where the attributes from a tag should appear in the component's template.
1. In general, you should place `...attributes` after any attributes you specify to give people using your component an opportunity to override your attribute. If `...attributes` appears after an attribute, it overrides that attribute. If it appears before an attribute, it does not.
1. The `class` attribute is special, and will be merged with any existing classes on the element rather than overwriting them. This allows you to progressively add CSS classes to your components, and makes them more flexible overall.

## Conditional Content

### if

1. Block:

```javascript
{{#if this.thingIsTrue}}
  Content for the block form of "if"
{{/if}}
```

Usage:

```javascript
{{#if condition}}
  {{!-- some content --}}
{{else}}
  {{!-- some other content --}}
{{/if}}

{{#if condition1}}
  ...
{{else if condition2}}
  ...
{{else if condition3}}
  ...
{{else}}
  ...
{{/if}}
```

2. Inline
   Sometimes, you will want to conditionally set an argument or attribute.

   ```javascript
   <div class={{if this.thingIsTrue "value-if-true" "value-if-false"}}>
   ```

   When passing a literal JavaScript value to a component, we have to wrap the value in double curlies (e.g. @isActive={{true}}). A value that isn't wrapped in curlies is assigned as string, which matches the behavior in HTML attributes. For example, writing @isActive=true will set @isActive to the string 'true'.

   Usage:
   if: `{{if condition value}}`
   if else: `{{if condition value1 value2}}`

## Block Content

Component templates can leave one or more placeholders that users can fill with their own HTML. These are called blocks.
Example:

```javascript
<ExampleComponent>
  This is the default <b>block content</b> that will
  replace `{{yield}}` (or `{{yield to="default"}}`)
  in the `ExampleComponent` template.
</ExampleComponent>
```

Through Block Content, users of the component can add additional styling and behavior by using HTML, modifiers, and other components within the block.

{{yield}} is named after a similar concept in scripting languages, including Ruby, JavaScript and Python. You don't need to understand the connection in order to use it, but if you're in the mood for a tangent, check out the yield operator in JavaScript

You can think of using {{yield}} as leaving a placeholder for the content of the Component tag.

### Conditional Blocks

Sometimes, we may want to provide some default content if the user of a component hasn't provided a block.

```javascript
error-dialog.hbs
<dialog>
  {{#if (has-block)}}
    {{yield}}
  {{else}}
    An unknown error occurred!
  {{/if}}
</dialog>
```

If we use `<ErrorDialog/>` it will render:

```html
<dialog>An unknown error occurred!</dialog>
```

### [Block Parameters](https://guides.emberjs.com/release/components/block-content/#toc_block-parameters)

Blocks can also pass values back into the template, similar to a callback function in JavaScript.
Example:

```javascript
<h1>{{@post.title}}</h1>
<h2>{{@post.author}}</h2>

{{@post.body}}

<!-- usage -->
<BlogPost @post={{@blogPost}} />
```

### [Named Blocks](https://guides.emberjs.com/release/components/block-content/#toc_named-blocks)

If you want to yield content to different spots in the same component, you can use named blocks. You just need to specify a name for the yielded block, like this: `{{yield to="somePlace"}}`
Example:

```javascript
popover.hbs

<div class="popover">
  <div class="popover__trigger">
    {{yield this.isOpen to="trigger"}}
  </div>
  {{#if this.isOpen}}
    <div class="popover__content">
      {{yield to="content"}}
    </div>
  {{/if}}
</div>

Usage:
<Popover>
  <:trigger as |open|>
    <button type="button">Click to {{if open "close" "open"}}  the popover!</button>
  </:trigger>
  <:content>
     This is what is shown when I'm opened!
  </:content>
</Popover>
```

A yielded block without a name is called default:

```javascript
<Card>
  <:title>
    <h3>It's nice to have me. Sometimes</h3>
  </:title>
  <:default>
    The card content will appear here!
  </:default>
</Card>

```
