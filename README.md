# heroku-buildpack-lerna

This is a fork of [heroku-buildpack-lerna](https://github.com/Zefir-Engineering/heroku-buildpack-lerna) that incorporates some logic from a [heroku-buildpack-monorepo fork](https://github.com/jan-tee/heroku-buildpack-monorepo/blob/master/bin/compile) to enable intra-repo dependencies.
It's currently highly-specific for the Belle Puzzles bot deployment, but it should be possible to remove some of the hard-coded variables using
lerna commands along the lines of the approach mentioned [here](https://github.com/lerna/lerna/issues/1061#issuecomment-336413280).

Buildpack allowing us to deploy an application using [lerna](https://github.com/lerna/lerna) on heroku.
This buildpack is based on these two buildpacks: [heroku-buildpack-nodejs](https://github.com/heroku/heroku-buildpack-nodejs) and [heroku-buildpack-monorepo](https://github.com/lstoll/heroku-buildpack-monorepo).

## Usage

To deploy your application, you need to:

- Set the Config Var PACKAGE_NAME in Heroku to reflect the name of the package you want to deploy.
- Set the config Var PACKAGE_PATH in Heroku to your application path if your package is not located into the `packages` directory.
- Set the Config Var YARN_PRODUCTION to false.

This buildpack will just run two commands thanks to lerna to compile your application.
To start your application, a file named `Procfile` is required in your application.

```
lerna bootstrap --scope="${PACKAGE_NAME}" --include-dependencies

lerna run build --scope="${PACKAGE_NAME}" --include-dependencies --stream
```

Be sure to add this buildpack after heroku-buildpack-nodejs (so lerna cli will be available and the two commands above will work).

## Troubleshooting

If lerna is not found, make sure to have lerna as a dev dependency in your package.json at the root of you repository.
Also, you'll need to set YARN_PRODUCTION to false so lerna won't be prunned.
