# Folder Structure

[https://cli.emberjs.com/release/basic-use/folder-layout/](https://cli.emberjs.com/release/basic-use/folder-layout/)

| File/Directory | Purpose                                                                                                                                                                                                        |
| -------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `app/`         | This is where folders and files for models, components, routes, templates and styles are stored. The majority of the project code is in this folder. See the table below for details.                          |
| `config`       | The config directory contains the environment.js where you can configure settings for your app and targets.js where the browser build targets are set                                                          |
| `dist/`        | Contains the application's distributable output. Ember transpiles the code with Babel and concatenates the code into a file called <app-name>.js. The folder contents are deployed to the application's server |
| `public/`      | This directory will be copied verbatim into the root of the built application. Use this for assets that don’t have a build step, such as images or fonts                                                       |
| `tests/`       | Includes the application’s unit and integration tests, as well as various helpers to load and run the tests                                                                                                    |
| `vendor`       | External dependencies not installed with npm                                                                                                                                                                   |

## Layout within app directory

| File/directory                       | Purpose                                                                                                                                                                                       |
| ------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `app/app.js`                         | The application’s entry point. This is the first executed module                                                                                                                              |
| `app/index.html`                     | The only page of the generated single-page app. Includes dependencies, and kickstarts your Ember application.                                                                                 |
| `app/router.js`                      | The applications route configuration. The routes defined here correspond to routes in app/routes/                                                                                             |
| `app/styles/`                        | Contains the stylesheets, whether SASS, LESS, Stylus, Compass, or plain CSS (though only one type is allowed, see Asset Compilation). These are all compiled into /dist/assets/<app-name>.css |
| `app/templates/`                     | The application's HTMLBars templates. These are compiled to /dist/assets/<app-name>.js                                                                                                        |
| `app/controllers/, app/models/, etc` | The JavaScript files or modules that contain the applications logic                                                                                                                           |

## Naming conventions

1. File and directory names use kebab-case with lowercase letters and a hyphen between words.
1. Components: PascalCase is used for components. For example, the nav-bar component would be resolved from the PascalCase component name in a template. <NavBar />
1. Component files can also be nested. You will need to use a special syntax to resolve a nested component. For example, if the nav-bar component were in the ui directory, <UI::NavBar />
1. Tests: When you use Ember CLI, Ember automatically creates a test file and suffixes the name with -test.js. If you want to manually create the file, make sure to suffix the name with -test.js so that your tests can run.
