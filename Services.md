# Services

1. A Service is an Ember object that lives for the duration of the application, and can be made available in different parts of your application.
2. Services are useful for features that require shared state or persistent connections. Example uses of services might include:
   1. User/session authentication.
   2. Geolocation.
   3. WebSockets.
   4. Server-sent events or notifications.
   5. Server-backed API calls that may not fit EmberData.
   6. Third-party APIs.
   7. Logging.

## Defining Services

1. Services can be generated using Ember CLI's service generator.
2. Services must extend the Service base class
3. Like any Ember object, a service is initialized and can have properties and methods of its own.

## Accessing Services

1. To access a service, you can inject it into any container-resolved object such as a component or another service using the service decorator from the @ember/service module.
2. There are two ways to use this decorator. You can either invoke it with no arguments, or you can pass it the registered name of the service.
3. When no arguments are passed, the service is loaded based on the name of the decorated property. You can load the shopping cart service with no arguments like below:

   1. ```javascript
      import Component from "@glimmer/component";
      import { service } from "@ember/service";

      export default class CartContentsComponent extends Component {
        // Will load the service defined in: app/services/shopping-cart.js
        @service shoppingCart;
      }
      ```

4. Another way to inject a service is to provide the name of the service as an argument to the decorator.

   ```javascript
   import Component from "@glimmer/component";
   import { service } from "@ember/service";

   export default class CartContentsComponent extends Component {
     // Will load the service defined in: app/services/shopping-cart.js
     @service("shopping-cart") cart;
   }
   ```

5. Sometimes a service may or may not exist, like when an initializer conditionally registers a service. Since normal injection will throw an error if the service doesn't exist, you must look up the service using Ember's getOwner instead:

   1. ```javascript
      import Component from "@glimmer/component";
      import { getOwner } from "@ember/application";

      export default class CartContentsComponent extends Component {
        // Will load the service defined in: app/services/shopping-cart.js
        get cart() {
          return getOwner(this).lookup("service:shopping-cart");
        }
      }
      ```
