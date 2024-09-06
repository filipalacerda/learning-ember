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
