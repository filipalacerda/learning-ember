# Routing

1. A route handler can do several things:
   1. It can render a template.
   2. It can load a model that is then available to the template.
   3. It can redirect to a new route, such as if the user isn't allowed to visit that part of the app.
   4. It can handle actions that involve changing a model or transitioning to a new route.
2. Defining routes:
   1. `ember generate route route-name`
3. Basic Routes:
   ```javascript
   Router.map(function () {
     this.route("about", { path: "/about" });
     this.route("favorites", { path: "/favs" });
   });
   ```
4. Nested Routes
   ```javascript
   Router.map(function () {
     this.route("posts", function () {
       this.route("new");
     });
   });
   ```
5. Index routes: The index route is most helpful for rendering a view when the route has dynamic segments defined in it or there are nested routes
6. Dynamic segments: A dynamic segment is a portion of a URL that starts with a : and is followed by an identifier:

```javascript
Router.map(function () {
  this.route("posts");
  this.route("post", { path: "/post/:post_id" });
});
```

7. Wildcard routes: You can define wildcard routes that will match multiple URL segments. This could be used, for example, if you'd like a catch-all route which is useful when the user enters an incorrect URL not managed by your app. Wildcard routes begin with an asterisk:
   ```javascript
   Router.map(function () {
     this.route("not-found", { path: "/*path" });
   });
   ```
   ```
   app/templates/not-found.hbs
   {{page-title "Not found"}}
   <p>Oops, the page you're looking for wasn't found</p>
   ```
8. Transitioning Between Routes:
   1. From a template, use <LinkTo /> as mentioned above
   2. From anywhere else in your application, such as a component, inject the Router Service and use the transitionTo() method
9. Query Params: The @query argument, along with the {{hash}} helper, can be used to set query params on a link:

   ```javascript
     // Explicitly set target query params
     <LinkTo @route="posts" @query={{hash direction="asc"}}>Sort</LinkTo>

     // Binding is also supported
     <LinkTo @route="posts" @query={{hash direction=this.otherDirection}}>Sort</LinkTo>
   ```

10. HTML Attributes:
    ```javascript
        <LinkTo @route="photos" class="btn btn-primary" role="button" aria-pressed="false">
        Discard Changes
      </LinkTo>
    ```

## Specifying a Route's Model:

1.  **A route's JavaScript file is one of the best places in an app to make requests to an API.**
2.  In Ember, functions that automatically run during rendering or setup are commonly referred to as "hooks". When a user first visits the /favorite-posts route, the model hook in app/routes/favorite-posts.js will automatically run.
3.  `model` hooks have some special powers:
    1. When you return data from this model, it becomes automatically available in the route's .hbs file as @model and in the route's controller as this.model.
    2. A model hook can return just about any type of data, like a string, object, or array, but the most common pattern is to return a JavaScript Promise.
    3. If you return a Promise from the model hook, your route will wait for the Promise to resolve before it renders the template.
    4. Since the model hook is Promise-aware, it is great for making API requests (using tools like fetch) and returning the results.
    5. When using the model hook to load data, you can take advantage of other niceties that Ember provides, like automatic route transitions after the data is returned, loading screens, error handling, and more. [more info](https://guides.emberjs.com/v5.11.0/routing/loading-and-error-substates/)
    6. The model hook may automatically re-run in certain conditions
4.  Behind the scenes, what is happening is that the [route's controller](https://api.emberjs.com/ember/release/classes/Route/methods/setupController?anchor=setupController) receives the results of the model hook, and Ember makes the model hook results available to the template.
5.  Fetch example:

    1. ```javascript
       import Route from "@ember/routing/route";
       import fetch from "fetch";
       export default class PhotosRoute extends Route {
         async model() {
           const response = await fetch("/my-cool-end-point.json");
           const photos = await response.json();

           return { photos };
         }
       }
       ```

6.  Multiple models:

    1. Multiple models can be returned through an RSVP.hash. The RSVP.hash method takes an object containing multiple promises.
    2. If all of the promises resolve, the returned promise will resolve to an object that contains the results of each request. For example:
    3. ```javascript
       import Route from "@ember/routing/route";
       import { service } from "@ember/service";
       import RSVP from "rsvp";
       export default class SongsRoute extends Route {
         @service store;

         model() {
           return RSVP.hash({
             songs: this.store.findAll("song"),
             albums: this.store.findAll("album"),
           });
         }
       }
       ```

    4. In the songs template, we can specify both models and use the {{#each}} helper to display each record in the song model and album model:
       1. ```javascript
          <ul>
            {{#each @model.songs as |song|}}
              <li>{{song.name}} by {{song.artist}}</li>
            {{/each}}
          </ul>
            <ul>
              {{#each @model.albums as |album|}}
                <li>{{album.title}} by {{album.artist}}</li>
              {{/each}}
            </ul>
          ```

7.  Dynamic models:

    1.  In Ember, this can be accomplished by defining routes with dynamic segments, or by using query parameters, and then using the dynamic data to make requests.
    2.  ```javascript
        Router.map(function () {
          this.route("posts");
          this.route("post", { path: "/post/:post_id" });
        });
        ```
    3.  In the model hook for routes with dynamic segments, it's your job to turn the ID (something like 47 or post-slug) into a model that can be rendered by the route's template.
    4.  ```javascript
        import Route from "@ember/routing/route";
        import { service } from "@ember/service";

        export default class PostRoute extends Route {
          @service store;

          model(params) {
            return this.store.findRecord("post", params.post_id);
          }
        }
        ```

    5.  Linking to a dynamic segment:
        1.  ```javascript
            {{#each @model as |photo|}}
              <LinkTo @route="photo" @model={{photo.id}}>
                link text to display
              </LinkTo>
            {{/each}}
            ```
    6.  **However, if you provide the entire model context, the model hook for that URL segment will not be run. For this reason, many Ember developers choose to pass only ids to <LinkTo> so that the behavior is consistent.**

8.  Reusing Route Context

    1.  Sometimes you need to fetch a model, but your route doesn't have the parameters, because it's a child route and the route directly above or a few levels above has the parameters that your route needs. You might run into this if you have a URL like /album/4/songs/18, and when you're in the songs route, you need an album ID.
    2.  In this scenario, you can use the paramsFor method to get the parameters of a parent route.

        1.  ```javascript
            import Route from "@ember/routing/route";
            import { service } from "@ember/service";

            export default class AlbumIndexRoute extends Route {
              @service store;

              model() {
                let { album_id } = this.paramsFor("album");

                return this.store.query("song", { album: album_id });
              }
            }
            ```

        2.  Using paramsFor will also give you the query params defined on that route's controller. This method could also be used to look up the current route's parameters from an action or another method on the route, and in that case we have a shortcut: this.paramsFor(this.routeName)

9.  Debugging models:
    1.  Use the {{debugger}} or {{log}} helper to inspect the {{@model}} from the template
10. Redirecting:
    1.  Transitioning Before the Model is Known: Since a route's beforeModel() executes before the model() hook, it's a good place to do a redirect if you don't need any information that is contained in the model.
    2.  Transitioning After the Model is Known: If you need information about the current model in order to decide about redirection, you can use the afterModel() hook. It receives the resolved model as the first parameter and the transition as the second one.
    3.  Child Routes:
        1.  If we redirect to posts.post in the afterModel hook, afterModel essentially invalidates the current attempt to enter this route. So the posts route's beforeModel, model, and afterModel hooks will fire again within the new, redirected transition. This is inefficient, since they just fired before the redirect.
        2.  Instead, we can use the redirect() method, which will leave the original transition validated, and not cause the parent route's hooks to fire again
11. Loading / Error Substates:

    1.  The error and loading substates exist as a part of each route, so they should not be added to your router.js file. To utilize a substate, the route, controller, and template may be optionally defined as desired
    2.  Loading:
        1.  During the beforeModel, model, and afterModel hooks, data may take some time to load. Technically, the router pauses the transition until the promises returned from each hook fulfill.
        2.  Simply define a template called loading (and optionally a corresponding route) that Ember will transition to.
        3.  The intermediate transition into the loading substate happens immediately (synchronously), the URL won't be updated, and, unlike other transitions, the currently active transition won't be aborted.
        4.  Ember will alternate trying to find a routeName-loading or loading template in the hierarchy starting with user.about.slow-model-loading:
    3.  Loading event:

        1.  If the various beforeModel/model/afterModel hooks don't immediately resolve, a loading event will be fired on that route.
        2.  ```javascript
            import Route from "@ember/routing/route";
            import { service } from "@ember/service";
            import { action } from "@ember/object";

            export default class UserSlowModelRoute extends Route {
              @service store;

              model() {
                return this.store.findAll("slow-model");
              }

              @action
              loading(transition, originRoute) {
                let controller = this.controllerFor("foo");
                controller.set("currentlyLoading", true);
                return true; // allows the loading template to be shown
              }
            }
            ```

        3.  If the loading handler is not defined at the specific route, the event will continue to bubble above a transition's parent route, providing the application route the opportunity to manage i

    4.  Error substates:
        1.  the default error handlers will look for an appropriate error substate to enter, if one can be found.
    5.  Error event:
        1.  If the route's model hook returns a promise that rejects (for instance the server returned an error, the user isn't logged in, etc.), an error event will fire from that route and bubble upward.
        2.  This error event can be handled and used to display an error message, redirect to a login page, etc.

12. Query Parameters
    1.  Specifying Query Parameters: Query params are declared on route-driven controllers
    2.  [Query Parameters](https://guides.emberjs.com/v5.11.0/routing/query-params/)
13. Asynchronous Routing
    1.  Ember's approach to handling asynchronous logic in the router makes heavy use of the concept of Promises. In short, promises are objects that represent an eventual value.
    2.  When transitioning between routes, the Ember router collects all of the models (via the model hook) that will be passed to the route's controllers at the end of the transition.
    3.  If the model hook (or the related beforeModel or afterModel hooks) return normal (non-promise) objects or arrays, the transition will complete immediately.
    4.  But if the model hook (or the related beforeModel or afterModel hooks) returns a promise (or if a promise was provided as an argument to transitionTo), the transition will pause until that promise fulfills or rejects.

### Controlers

1. a Controller is a routable object which receives a single property from the Route - model - which is the return value of the Route's model() methos.
2. The model is passed from the Route to the Controller by default using the setupController() function.
3. The Controller is then often used to decorate the model with display properties such as retrieving the full name from a name model.
4. A Controller is usually paired with an individual Route of the same name.
5. Routing Query Parameters should be defined within a controller
6. We only need to generate a Controller file if we want to customize the properties or provide any actions to the Route. If we have no customizations, Ember will provide a default Controller instance for us at run time.

#### Where and When to use Controllers?

1. Controllers are used as an extension of the model loaded from the Route. Any attributes or actions that we want to share with components used on that Route could be defined on the Controller and passed down through the Route’s template.
2. Controllers are singletons so we should avoid keeping state that does not derive from either the Model or Query Parameters since these would persist in between activations such as when a user leaves the Route and then re-enters it.
3. Controllers can also contain actions, Query Parameters, Tracked Properties, and more.

**When should we create a Controller?**

1. We want to pass down actions or variables to share with a Route’s child components
1. We need to compute a value based on the results of the model hook
1. We need to support query parameters
