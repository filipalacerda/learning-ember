## Invokables

1. In Ember templates, “invokables” are things you can invoke in a template. These include components, helpers, and modifiers.

### Signature Basics

1. Ember template invokables have a shared set of potential API features, each of which is captured in the signature:
   1. `Args` — the arguments the invokable accepts (which may be positional or named)
   2. `Return` — the value(s) the invokable returns
   3. `Blocks` — the block(s) yielded by the invokable
   4. `Element` — the element associated with the invokable
   ```javascript
    Interface InvokableSignature {
      Args?: {
        Positional?: Array<unknown>;
        Named?: {
          [argName: string]: unknown;
        };
      };
      Return?: unknown;
      Blocks?: {
        [blockName: string]: {
          Params?: {
            Positional?: Array<unknown>;
          };
        };
      };
      Element?: Element | null;
    }
   ```

### Glimmer components

1. Glimmer Components are defined in one of three ways:
   1. with templates only,
   2. with a template and a backing class,
   3. or with only a backing class (i.e. a provider component).
2. When using a backing class, you get a first-class experience using TypeScript with a component signature. For type-checking Glimmer templates as well, see Glint.
   1. The normal form of a Glimmer component signature is
   2. ```javascript
      interface ComponentSignature {
        Args: {
          [argName: string]: unknown,
        };
        Blocks: {
          [blockName: string]: Array<unknown>,
        };
        Element: Element;
      }
      ```

#### Helpers

1. Helpers in Ember are just functions or classes with a well-defined interface, which means they largely Just Work™ with TypeScript. However, there are a couple things you'll want to watch out for.
2. The signature for a helper includes its named and/or positional arguments and its return type:

```javascript
interface HelperSignature {
  Args?: {
    Positional?: Array<unknown>,
    Named?: {
      [argName: string]: unknown,
    },
  };
  Return?: unknown;
}
```

3. Function-based helpers: You never have to write a signature when working with function-based helpers—whether using a standalone function as a helper or using the legacy helper() definition
   1. Instead, you can write a function definition as usual, providing types and documentation for its arguments and return types the way you would any other function.
   2. Tools like Glint and documentation generators can synthesize all the information required from those definitions, and in general work better with normal function definitions than with signatures for function-based helpers.
4. Class-based helpers: Signatures are more useful for class-based helpers, where they are the only way to provide the type information for Glint/TypeScript.

#### Modifiers

1. The signature for a modifier consists of any named or positional arguments along with the kind of element it can be applied to. The arguments are optional, but the element is required.
2. ```javascript
   interface ModifierSignature {
     Args?: {
       Positional?: Array<unknown>,
       Named?: {
         [argName: string]: unknown,
       },
     };
     Element: Element;
   }
   ```
3. Function-based modifiers: Function-based modifiers do not require writing out a signature manually. Instead, you can—and should!—write the types directly on the function which defines them.

#### Advanced signature techniques

1. We can also define signatures in more complicated ways using more advanced TypeScript features. Nearly anything you can do with a “regular” TypeScript function or class, you can also do with signatures for Glimmer invokables. We can make a component accept a generic type, or use union types. With these tools at our disposal, we can even define our signatures to make illegal states un-representable.

### TypeScript: Routing

1. Routes: Since Ember Routes are just regular JavaScript classes with a few special Ember lifecycle hooks and properties available, TypeScript should "Just Work." Ember's types supply the definitions for the various methods available within route subclasses, which will provide autocomplete and type-checking along the way.
2. Conrollers: The main thing to be aware of is special handling around query params. In order to provide type safety for query param configuration, Ember's types specify that when defining a query param's type attribute, you must supply one of the allowed types: 'boolean', 'number', 'array', or 'string'
3. Route Models:

   1. ```javascript
      import type Route from '@ember/routing/route';

      /** Get the resolved model value from a route. */
      export type ModelFrom<R extends Route> = Awaited<ReturnType<R['model']>>;
      ```

   2. How that works:
      1. `Awaited<P>` says "if this is a promise, the type here is whatever the promise resolves to; otherwise, it's just the value"
      2. `ReturnType<T>` gets the return value of a given function
      3. `R['model']` (where R has to be Route itself or a subclass) says "the property named model on Route R
   3. `ModelFrom<Route>` ends up giving you the resolved value returned from the model hook for a given route. We can use this functionality to guarantee that the `model` on a `Controller` is always exactly the type returned by `Route::model` by writing something like this:

      1. ```javascript
         import Controller from "@ember/controller";
         import MyRoute from "my-app/routes/my-route";
         import { ModelFrom } from "my-app/lib/type-utils";

         export default class ControllerWithModel extends Controller {
           declare model: ModelFrom<MyRoute>;
         }
         ```

   4. **The ModelFrom type utility only works if you do not mutate the model in either the afterModel or setupController hooks on the route! That's generally considered to be a bad practice anyway.**
