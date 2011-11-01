# Anatomy of a Play 2.0 application

## The standard application layout

The layout of a Play application is standardized to keep things as simple as possible.

#### The app/ directory

The `app` directory contains all executable artifacts: Java and Scala source code, templates as well as compiled assets sources.

There are three standard packages in the `app` directory, one for each layer of the MVC architectural pattern: 

- `app/controllers`
- `app/models`
- `app/views`

You can of course add your own packages like for example an `app/utils` package.

> Note that in Play 2.0, the controllers,models,views conventions are now really just conventions and can be changed if needed (like prefixing everything with com.yourcompany).

There is also an optional directory called `app/assets` containing compiled assets like [[Less sources | http://lesscss.org/]] or [[CofeeScript sources | http://jashkenas.github.com/coffee-script/]].

#### The public/ directory

Resources stored in the `public` directory are static assets and are served directly by the Web server.

This directory is split into three standard sub-directories: for images, CSS stylesheets and JavaScript files. You should try to organize your static assets like this to keep all Play applications consistent.

> In default created application the `/public` directory is mapped to the `/assets` URL path, but you can easily change that, or even use several directories for your static assets.

#### The conf/ directory

The `conf` directory contains all configuration files for the application.

There are two main configuration files:

- `application.conf`, the main configuration file for the application. It contains standard configuration parameters.
- `routes`, the routes definition file.

If you need to add some configuration options specific to your application, it’s a good idea to add more options to the `application.conf` file.

If any library needs a specific configuration file, try to file it under the conf directory.