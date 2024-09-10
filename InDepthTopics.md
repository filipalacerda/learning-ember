## Autotracking

1. Autotracking is how Ember's reactivity model works - how it decides what to rerender, and when.

### Autotracking Basics

1. When Ember first renders a component, it renders the initial state of that component - the state of the instance, and state of the arguments that are passed to it
2. By default, Ember assumes that none of the values that are rendered will ever change. These are static, state-less parts of the template.
3. In order to tell Ember a value might change, we need to mark it as trackable. Trackable values are values that
   1. Can change over their component’s lifetime and
   2. Should cause Ember to rerender if and when they change
4. If these values change in the future, it schedules a rerender, and then updates only the values that could have changed.

### Updating Tracked Properties

1. Tracked properties can be updated like any other property, using standard JavaScript syntax.
2. For instance, we could update a tracked property via an action
3. Another way that a tracked property could be updated is asynchronously, if you're sending a request to the server.
4. Tracking Through Methods: tracking works through methods or functions as wel
5. Tracked Properties in Custom Classes: Tracked properties can also be applied to your own custom classes, and used within your components and routes

### Caching of tracked properties

1. Tracked properties are not cached
2. A tracked property can also be recomputed even though its dependencies haven't changed
3. If the computation that happens in the getter is very expensive, however, you will want to cache the value and retrieve it when the dependencies haven't changed. You want to recompute only if a dependency has been updated.
4. Ember's @cached decorator lets you cache (or "memoize") a getter by simply marking it as `@cached`
5. **In general, you should avoid using @cached unless you have confirmed that the getter you are decorating is computationally expensive, since @cached adds a small amount of overhead to the getter.**

## Making API Requests

1. Where to make API requests: API requests can be made almost anywhere in an Ember app, however the most common place is the model hook of a Route.
2. Requests in a Route's model hook: In almost every case, this is where your app should load data.
   1. These are the main reasons to load data in a model hook:
      1. Respecting the URL results in better user experience
      2. Proper use of loading and error states result in better user experience
      3. Dealing with concurrency is a big source of bugs
      4. The router is designed to solve those problems for you in the majority of common situations
3. Requests in Components: The drawback is that requires more work from developers to handle async, rendering, errors, concurrency, and URL state themselves.
   1. However there are valid use cases for loading data in a component, for developers who are comfortable handling the router's features themselves
   2. Some common use cases include:
      1. UI elements whose data is independent of a route. For example, a modal that could appear on many different routes, and the modal has its own unique data.
      2. loading data in parallel within deeply nested routes
      3. highly interactive loading, like a search bar with its own loading state, error handling, etc.
4. Requests in Services: If someone is connecting to a third-party API, such as a service for payment or mapping, and they need that state across many routes, a Service might be a good place to make requests. Some common use cases include polling for data and managing websocket connections.
   1. Requests in services have the same drawbacks as Components. Functions and state in a Service can be used almost anywhere in the app.

## Native Classes

1. Defining Classes
   1. constructor:
      1. The constructor method is a special method in classes. It's run when you create a new instance of the class, and can be used to setup the class
      2. You can also pass arguments to the constructor when creating instances with new
      3. The constructor can't be called in any other way. It doesn't exist on the class or instances
   2. Methods
      1. Methods are functions that are defined on the class, and usable by instances
      2. Like functions declared on objects, they can access the instance using this, so they can store and access variables on the instance.
      3. Methods do not exist on the class itself by default. They exist on the class's prototype, and are only readily callable by instances. However, they can be added to the class directly using the static keyword
   3. Fields
      1. Class fields allow you to assign properties to an instance of the class on construction.
      2. Class fields are somewhat like object properties, but they have some key differences. They are created and assigned to every instance of the class, meaning that instance gets a unique version of the field. This doesn't matter if the field is a primitive, like a string or a number, but does matter if it's an object or an array
      3. Fields can also access the class instance using this when they are being assigned
      4. relying on state should generally be avoided in field initializers, since it can make your classes brittle and error prone, especially when refactoring
      5. Fields are assigned before any code in the constructor method is run, which is why we can rely on them being assigned correctly by the time it runs.
      6. As with methods, fields do not exist on the class itself, nor do they exist on the class's prototype, they only exist on the instance of the class. However, they can be added to the class directly using the static keyword
   4. Accessors:
      1. Accessors, also known as getters/setters, allow you to define a special function that is accessed like a property
      2. Generally, the setter function stores the value somewhere, and the getter function retrieves it
      3. Getters can also be used on their own to calculate values dynamically. These values are recalculated every time the property is accessed.
      4. This is why getters should generally avoid mutating state on the instance, and you should be aware of their performance cost since they'll rerun the code every time.
   5. `static`
      1. Static class elements are not available on instances, and are only available directly on the class itself
   6. Decorators
      1. Decorators are user defined modifiers that can be applied to a class or class element such as a field or method to change its behavior.
      2. Decorators are normal JavaScript functions that get applied with a special syntax, which is why you import them like any other function, but you use the @ symbol when applying them
      3. Ember provides a number of decorators, such as the @tracked decorator, that will be described in greater detail later on in the guides.
   7. [Extending classes](https://guides.emberjs.com/v5.11.0/in-depth-topics/native-classes-in-depth/#toc_extending-classes)

## Rendering Values

TODO: https://guides.emberjs.com/v5.11.0/in-depth-topics/rendering-values/
