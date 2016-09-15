## Istanbul instrumenter loader for [webpack](https://webpack.github.io/)

[![npm](http://img.shields.io/npm/v/istanbul-instrumenter-loader.svg?style=flat-square)](https://www.npmjs.org/package/istanbul-instrumenter-loader)
[![deps](http://img.shields.io/david/deepsweet/istanbul-instrumenter-loader.svg?style=flat-square)](https://david-dm.org/deepsweet/istanbul-instrumenter-loader#info=dependencies)

Instrument JS files with [Istanbul](https://github.com/gotwarlost/istanbul) for subsequent code coverage reporting.

### Install

```sh
$ npm i -D istanbul-instrumenter-loader
```

### Setup

#### References

* [Using loaders](https://webpack.github.io/docs/using-loaders.html)
* [karma-webpack](https://github.com/webpack/karma-webpack#karma-webpack)
* [karma-coverage](https://github.com/karma-runner/karma-coverage#configuration)

#### Project structure

Let's say you have the following:

```
├── src/
│   └── components/
│       ├── bar/
│       │   └── index.js
│       └── foo/
│           └── index.js
└── test/
    └── src/
        └── components/
            └── foo/
                └── index.js
```

To create a code coverage report for all components (even for those for which you have no tests yet) you have to require all the 1) sources and 2) tests. Something like it's described in ["alternative usage" of karma-webpack](https://github.com/webpack/karma-webpack#alternative-usage):

#### test/index.js

```js
// require all `test/components/**/index.js`
const testsContext = require.context('./src/components/', true, /index\.js$/);

testsContext.keys().forEach(testsContext);

// require all `src/components/**/index.js`
const componentsContext = require.context('../src/components/', true, /index\.js$/);

componentsContext.keys().forEach(componentsContext);
```

This file will be the only entry point for Karma:

#### karma.conf.js

```js
config.set({
    …
    files: [
        'test/index.js'
    ],
    preprocessors: {
        'test/index.js': 'webpack'
    },
    webpack: {
        …
        module: {
            preLoaders: [
                // instrument only testing sources with Istanbul
                {
                    test: /\.js$/,
                    include: path.resolve('src/components/'),
                    loader: 'istanbul-instrumenter'
                }
            ]
        }
        …
    },
    reporters: [ 'progress', 'coverage' ],
    coverageReporter: {
        type: 'text'
    },
    …
});
```

#### Options
The loader supports all options supported by `istanbul-lib-instrument`. 

The default options are

```js
coverageVariable: "__coverage__",
preserveComments: false,
compact: true,
esModules: false,
autoWrap: true,
produceSourceMap: false,
sourceMapUrlCallback: null,
debug: false
```

E.g. if a project using ES6 modules is instrumented, the `esModules` options must be set to `true`:

```js
{
	module: {
	  postLoaders: [
	    {
				test: /\.ts$/,
				loader: "istanbul-instrumenter?esModules=true",
				include: path.resolve("./src"),
			}
		]
	}
}
```
        
### License
[WTFPL](http://www.wtfpl.net/wp-content/uploads/2012/12/wtfpl-strip.jpg)
