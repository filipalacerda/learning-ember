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

## Patterns for Components

### Argument Defaults

1. At some point, you may want to add default values to your arguments if one wasn't passed to your component.
2. Arguments are not mutable, so if you attempt to reassign a value on this.args, it'll fail
3. Instead, you should define a getter on your component that provides the default value if the argument was not provided.
4. For instance, if you wanted to create a tooltip icon that had a standard icon and class, you could do it like so:

   ```javascript
   import Component from "@glimmer/component";

   export default class TooltipComponent extends Component {
     get icon() {
       return this.args.icon ?? "icon-info";
     }

     get tooltipClass() {
       return this.args.tooltipClass + " tooltip";
     }
   }

   <div class={{this.tooltipClass}}>
      <i class={{this.icon}}></i>
      {{@content}}
   </div>
   ```

5. Note that because arguments are prefixed with @ in templates, and placed on args in the component definition, we can use the same name for our icon and tooltipClass getters, which is pretty convenient.
6. We can also tell clearly when we look at the template for the tooltip that this.tooltipClass and this.icon are values that come from the class definition, and that means they probably have been used in some kind of dynamic code (in this case, our defaulting logic).

### Attributes

1. The positioning of ...attributes matters, with respect to the other attributes in the element it is applied to.
2. Attributes that come before ...attributes can be overridden, but attributes that come after cannot.
3. There is one exception to this, which is the class attribute. class will get merged, since its more often the case that users of the component want to add a class than completely override the existing ones. For class, the order of ...attributes will determine the order of merging.

### Contextual Components

1. The {{component}} helper can be used to defer the selection of a component to runtime. The <MyComponent /> syntax always renders the same component, while using the {{component}} helper allows choosing a component to render on the fly.
2. This is useful in cases where you want to interact with different external libraries depending on the data. Using the {{component}} helper would allow you to keep different logic well separated.
3. The first parameter of the helper is the name of a component to render, as a string. So {{component 'blog-post'}} is the same as using <BlogPost />.
4. The real value of {{component}} comes from being able to dynamically pick the component being rendered.

Full guide [here](https://emberjs-1.gitbook.io/ember-component-patterns)

## Patterns for Actions

1. Check tracked properties [guide](https://guides.emberjs.com/release/in-depth-topics/autotracking-in-depth/)
2. Check the [guide](https://guides.emberjs.com/release/in-depth-topics/patterns-for-actions/)

## Looping through Lists

1. add an {{each}} helper to the template
2. {{each}} will receive each message as its first block param, and we can use that item in the template block for the loop.

```javascript
{{#each this.messages as |message|}}
    <Message
      @username={{message.username}}
      @userIsActive={{message.active}}
      @userLocaltime={{message.localTime}}
    >
      {{{message.content}}}
    </Message>
  {{/each}}
```

3. !! Notice that we used triple curly brackets around {{{message.content}}}. This is how Ember knows to insert the content directly as HTML, rather than directly as a string. -> **Triple curly brackets are a convenient way to put dynamic HTML into Ember templates, but are not recommended for production apps.**

#### Item Indexes

1. The index of each item in the array is provided as a second block param. This can be useful at times if you need the index, for instance if you needed to print positions in a queue

```javascript
{{#each this.queue as |person index|}}
```

#### Empty Lists

1. The {{#each}} helper can also have a corresponding {{else}}. The contents of this block will render if the array passed to {{#each}} is empty:

```
{{#each this.people as |person|}}
  Hello, {{person.name}}!
{{else}}
  Sorry, nobody is here.
{{/each}}
```

### Looping Through Objects

1. here are also times when we need to loop through the keys and values of an object rather than an array,
2. We can use the {{#each-in}} helper to do this:

```javascript
 categories = {
    'Bourbons': ['Bulleit', 'Four Roses', 'Woodford Reserve'],
    'Ryes': ['WhistlePig', 'High West']
  };

<ul>
  {{#each-in this.categories as |category products|}}
    <li>{{category}}
      <ol>
        {{#each products as |product|}}
          <li>{{product}}</li>
        {{/each}}
      </ol>
    </li>
  {{/each-in}}
</ul>

```

3. The first block parameter (category in the above example) is the key for this iteration, while the second block parameter (products) is the actual value of that key.

#### Ordering

1. An object's keys will be listed in the same order as the array returned from calling Object.keys on that object. If you want a different sort order, you should use Object.keys to get an array, sort that array with the built-in JavaScript tools, and use the {{#each}} helper instead.

#### Empty lists

1. The {{#each-in}} helper can have a matching {{else}}. The contents of this block will render if the object is empty, null, or undefined:

```
{{#each-in this.people as |name person|}}
  Hello, {{name}}! You are {{person.age}} years old.
{{else}}
  Sorry, nobody is here.
{{/each-in}}
```

## Template Lifecycle, DOM, and Modifiers

1. For the most part, you should be able to build Ember applications without directly manipulating the DOM
2. The best way to think about your component's output is to assume that it will be re-executed from the top every time anything changes in your application.
3. The same philosophy that applies to changing text also applies to changing attributes

## Conditional Attributes

1. We could accomplish this requirement by using the if helper inside of an attribute `<div class={{if @isAdmin "superuser" "standard"}}>`

## Event Handlers

1. If you want to add an event handler to an HTML element, you can use the {{on element modifier.

```javascript
export default class CounterComponent extends Component {
  @tracked count = 0;

  @action
  increment() {
    this.count++;
  }
}

<button type="button" {{on "click" this.increment}}>
```

## Manipulating Properties

1. let's say you want to create an <audio> element, but pass it a blob as its srcObject.
2. Since srcObject is a property and not an HTML attribute, you can use the prop element modifier from ember-prop-modifier like this:

`<audio {{prop srcObject=this.blob}} />` 3. If you're looking at a piece of documentation written using HTML syntax, you can use the syntax as-is in your template, and use {{ to insert dynamic content. 4. On the other hand, if you're looking at JavaScript documentation that tells you to set a property on an element object, you can use {{prop to set the prop

5. If you want to set a property, you can use the prop element modifier.

## Calling Methods On First Render

1. let's say we want to focus an <input> in a form as soon as the form is rendered. The web API for focusing an element is: `inputElement.focus();`
2. This code needs to run after the element is rendered.
3. The simplest way to accomplish this is by using the `did-insert` modifier from @ember/render-modifiers.

```
<form>
  <input {{did-insert this.focus}}>
</form>
```

### Abstracting the Logic Into a Custom Modifier

1. Let's say we want to have this:

```
<form>
  <input {{autofocus}}>
</form>
```

2. Generate the autofocus modifier for your application:

`ember generate modifier autofocus` 3. Now add the functionality to focus the element:

```javascript
app / modifiers / autofocus.js;
import { modifier } from "ember-modifier";

export default modifier((element) => element.focus());
```

3. [Ember-modifier](https://github.com/ember-modifier/ember-modifier#usage)

## Communicating Between Elements in a Component

1. While we could manage these DOM interactions in the component class we're better off using a modifier here. It lets us cleanly separate our concerns: **the component manages the state, and the modifier manages interactions with the DOM.**
2. There are three reasons to reach for modifiers for DOM element interactions:

A component, by itself, doesn't have direct access to DOM elements. We have to render the page, push an element back up into the component class, and only then can we safely refer to that element. This can sometimes require us to render the component's HTML twice in order for things to start working. Modifiers let us avoid this possible performance issue.
By keeping state in the component and handling DOM method calls in a modifier, we can use autotracking and stick to 1-way data flow in the component. Further, we could change the component's own design later without having to change how we interact with the DOM element.
The code for calling the audio element's play and pause can be reused. It isn't tied to this particular audio component. It can be tested independently, too!

## [Out-of-Component Modifications](https://guides.emberjs.com/release/components/template-lifecycle-dom-and-modifiers/#toc_out-of-component-modifications)

## Built-in Components

1. Ember provides 2 components for building a form:

[<Input>](https://guides.emberjs.com/release/components/built-in-components/#toc_input)
[<Textarea>](https://guides.emberjs.com/release/components/built-in-components/#toc_textarea)

## Template Tag Format

1. The template tag format is a powerful, new way to write components in Ember. It's a single-file format that combines the component's JavaScript and Glimmer template code. The <template> tag is used to keep a clear separation between the template language and the JavaScript around it.
2. Template tag components use the file extension .gjs. This abbreviation is short for "Glimmer JavaScript". The file extension .gts is also supported for TypeScript components.
3. See more information [here](https://guides.emberjs.com/release/components/template-tag-format/)

### (New Capabilities)[https://guides.emberjs.com/release/components/template-tag-format/#toc_new-capabilities]

1. Locally-scoped values
2. Multiple components per file
3. Installation: https://guides.emberjs.com/release/components/template-tag-format/#toc_installation
