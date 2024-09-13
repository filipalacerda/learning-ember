# Applications and Instances

1. Every Ember application is represented by a class that extends `Application`. This class is used to declare and configure the many objects that make up your app.
2. As your application boots, it creates an ApplicationInstance that is used to manage its stateful aspects. This instance acts as the "owner" of objects instantiated for your app.
3. **Essentially, the Application defines your application while the ApplicationInstance manages its state.**

# Dependency Injection

1. Ember applications utilize the dependency injection ("DI") design pattern to declare and instantiate classes of objects and dependencies between them.
2. Generally, Services are Ember's primary method for sharing state via dependency injection.

## Overview

1. Applications and application instances each serve a role in Ember's DI implementation.
2. An Application serves as a "registry" for dependency declarations. Factories (i.e. classes) are registered with an application, as well as rules about "injecting" dependencies that are applied when objects are instantiated.
3. An ApplicationInstance serves as the "owner" for objects that are instantiated from registered factories. Application instances provide a means to "look up" (i.e. instantiate and / or retrieve) objects.

## Factory Registrations

1. A factory can represent any part of your application, like a route, template, or custom class.
2. Every factory is registered with a particular key. For example, the index template is registered with the key template:index, and the application route is registered with the key route:application.
3. Registration keys have two segments split by a colon (:). The first segment is the framework factory type, and the second is the name of the particular factory.
