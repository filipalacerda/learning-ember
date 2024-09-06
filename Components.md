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

## Helper Functions

Helper functions are JavaScript functions that you can call from your template.

Ember's template syntax limits what you can express to keep the structure of your application clear at a glance. When you need to compute something using JavaScript, you can use helper functions.

### Local helper functions

It's possible to use plain functions for helpers and modifiers. A plain helper function can be "local" to or defined on components and controllers.

Create a `.js` file with the same name as the template's component:

```javascript
export default class Message extends Component {
  substring = (string, start, end) => string.substring(start, end);
}
```

usage:
`@initial={{this.substring @username 0 1}}`

#### Named arguments

Helpers default to using positional arguments, but sometimes it can make the corresponding syntax {{substring @username 0 1}} a little hard to read. We see some numbers at the end but can't tell what exactly they mean. We can use named arguments to make the substring helper easier to read.:
`@initial={{substring @username start=0 end=1}}`

Helpers take named arguments as a JavaScript object. All named arguments are grouped into an "options object" as the last parameter:
`substring = (string, options) => string.substring(options.start, options.end);`

You can mix positional and named arguments to make your templates easy to read:

```javascript
hbs
{{this.calculate 1 2 op="add"}}
js
export default class Calculator extends Component {
  calculate(first, second, options) {
    // ...
  }
}
```

#### Nested Helpers

Sometimes, you might see helpers invoked by placing them inside parentheses, (). This means that a Helper is being used inside of another Helper or Component. This is referred to as a "nested" Helper Invocation. Parentheses must be used because curly braces {{}} cannot be nested:

`{{this.sum (this.multiply 2 4) 2}}`
In this example, we are using a helper to multiply 2 and 4 before passing the value into {{sum}}.

### Global Helper Functions

Ember provides a way to use global helpers. We define global helper functions in the app/helpers folder. Once defined, they will be available to use directly inside all templates in your app.

Example:

```javascript
app / helpers / substring.js;
export default function substring(string, start, end) {
  return string.substring(start, end);
}
```

#### Classic Helpers

Sometimes, you may encounter helpers defined using the helper function:

```javascript
app / helpers / substring.js;
import { helper } from "@ember/component/helper";

function substring(positional, { start, end }) {
  const string = positional[0];
  return string.substring(start || 0, end);
}

export default helper(substring);
```

By wrapping the function using the helper() function, Ember will extract the arguments passed from the template. It'll then call your function with an array (positional arguments passed in the template) and an object (named arguments passed in the template).
This style mostly exists for backwards compatibility reasons, but the other advantage is that it makes it easier to untangle the positional and named arguments

#### Class Helpers

Classic helpers can also be defined using class syntax. For instance, we could define the substring helper using classes instead.

```javascript
app / helpers / substring.js;
import Helper from "@ember/component/helper";

export default class Substring extends Helper {
  compute(positional, { start, end }) {
    const string = positional[0];
    return string.substring(start || 0, end);
  }
}
```

Class helpers are useful when the helper logic is fairly complicated, requires fine-grained control of the helper lifecycle, is stateful (we'll be discussing state in the next chapter), or requiring access to a service.

### [Built-in Helpers](https://guides.emberjs.com/release/components/helper-functions/#toc_built-in-helpers)

## Component State and Actions

While you can accomplish a lot in Ember using HTML templating, you'll need JavaScript to make your application interactive.

### Tracked properties

```javascript
import Component from "@glimmer/component";
import { tracked } from "@glimmer/tracking";

export default class CounterComponent extends Component {
  @tracked count = 0;
}
```

`@tracked count = 0` This line creates a dynamic value called count, which you can stick inside of the template instead of hard coding it.

When we use {{this.count}} in the component template, we're referring to a property that we defined in the JavaScript class.

### HTML Modifiers and Actions

To attach an event handler to an HTML tag, we use the `on` HTML modifier. HTML modifiers are an Ember syntax that allow us to attach logic to a tag.
`<button type="button" {{on "click" this.increment}}>`

To make those event handlers do something, we will need to define actions in the component JavaScript. An action is a JavaScript method that can be used from a template.

```javascript
@action
  increment() {
    this.count = this.count + 1;
  }
```

### Passing Arguments to Actions

```javascript
 @action
  change(amount) {
    this.count = this.count + amount;
  }
```

we'll update the template to turn the click handler into a function that passes an amount (for example, 1 and -1) in as an argument, using the fn helper:
`<button type="button" {{on "click" (fn this.change 1)}}>+1</button>`

An event handler takes a function as its second argument. When there are no arguments to the function, you can pass it directly, just like in JavaScript. Otherwise, you can build a function inline by using the fn syntax.

### Computed Values

Let's say we want to add a button to our counter that allows us to double the current count. Every time we press the button, the current count doubles.

Example:

1.  We'll add the multiple tracked property and an action called double that doubles the multiple.
2.  To get the multiplied number into the template, we'll use a JavaScript getter.
3.  ```javascript
    get total() {
        return this.count * this.multiple;
      }
    ```
4.  The getter does not need any special annotations. As long as you've marked the properties that can change with @tracked, you can use JavaScript to compute new values from those properties: `<p>= {{this.total}}</p>`
5.  You might have been tempted to make total a @tracked property and update it in the double and change actions. But this kind of "push-based" approach creates a lot of bugs. What happens if you create a new way to update multiple or amount properties and forget to update total at the same time?
6.  When you use getters and functions to derive the state you need, you're taking advantage of the benefits of declarative programming. In declarative programming, you describe what you need, not how to get it, which reduces the number of places where you can make mistakes.

#### Combining Arguments and State

1. Instead of allowing the component itself to be responsible for the multiple, let's allow it to be passed in.
1. In templates, we refer to arguments by prefixing them with the `@` sign (in this case `@multiple`). In order to compute this.total, we'll need to refer to the multiple argument from JavaScript.
1. We refer to a component's argument from JavaScript by prefixing them with `this.args`:

```javascript
get total() {
    return this.count * this.args.multiple;
  }
```

1. The total is now computed by multiplying a piece of local state (this.count) with an argument (this.args.multiple). You can mix and match local state and arguments however you wish, which allows you to easily break up a component into smaller pieces.

#### Combining Arguments and Actions

1. We can also pass actions down to components via their arguments, which allows child components to communicate with their parents and notify them of changes to state.

```javascript
 @action
  double() {
    this.args.updateMultiple(this.args.multiple * 2);
  }
```

1. Now, the Counter calls the updateMultiple argument (which we expect to be a function) with the new value for multiple, and the parent component can update the multiple.
   `<Counter @multiple={{this.multiple}} @updateMultiple={{this.updateMultiple}} />`

