# Marten Framework Heroku Buildpack

This is the official [Heroku buildback](https://devcenter.heroku.com/articles/buildpacks) for the Marten web framework.

## Requirements

### 1. Heroku CLI

The [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli) should be installed and properly configured.

### 2. Procfile

You should ensure that your project defines a [`Procfile`](https://devcenter.heroku.com/articles/procfile) file with at least the following content:

```procfile
web: bin/server --port \$PORT
```

If your application requires the use of a database, you should also add a [release process](https://devcenter.heroku.com/articles/procfile#the-release-process-type) that runs the Marten [`migrate`](https://martenframework.com/docs/development/reference/management-commands#migrate) management command to your `Procfile` file:

```procfile
web: bin/server --port \$PORT
release: marten migrate
```

## Usage

Once your setup meets all the [requirements](#requirements), you can create an application in Heroku using this buildpack by running the following `heroku create` command:

```bash
heroku create myapp --buildpack=git@github.com:martenframework/heroku-buildpack-marten.git
```

If you need to add this buildback to an existing application, you can use the following `heroku buildpacks` command:

```bash
heroku buildpacks:add https://github.com/martenframework/heroku-buildpack-marten
```

A few additional things should be noted regarding this buildpack:

* The compiled server binary will be placed under the `bin/server` path.
* Your project's `manage.cr` file will be compiled as well and will be available by simply calling the `marten` command. This means that you can run `marten <command>` if you need to call specific [management commands](https://martenframework.com/docs/development/management-commands).
* The buildpack will automatically cache Crystal and your project's installed shards. Note that the Crystal version used for your application will be determined by looking for a `.crystal-version` file at the root of your project: if this file is not found, the buildpack will default to using the latest Crystal release.
* The buildpack will automatically call the [`collectassets`](https://martenframework.com/docs/development/reference/management-commands#collectassets) management command in order to collect your project's [assets](https://martenframework.com/docs/assets/introduction) and copy them to your configured assets storage. You can set the `DISABLE_COLLECTASSETS` environment variable to `1` if you don't want this behavior.
* If you need to build and package assets _before_ the Marten buildpack is executed, you could use the official [Node.js buildpback](https://github.com/heroku/heroku-buildpack-nodejs) and define a "build" script in your project's `package.json` file. You should ensure that the index you assign to the Node.js buildpack is lower than the one assigned to the Marten buildpack. This will ensure that assets are built before they are collected by Marten.

## Acknowledgments

This buildpack is inspired by the [official Crystal buildpack](https://github.com/crystal-lang/heroku-buildpack-crystal) and essentially extends it to support Marten applications.

## Authors

Morgan Aubert ([@ellmetha](https://github.com/ellmetha)) and 
[contributors](https://github.com/martenframework/marten-sendgrid-emailing/contributors).

## License

MIT. See ``LICENSE`` for more details.
