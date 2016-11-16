# React Development Environment

In order for React to be processed by the browser, the JSX needs to be transpiled into regular JavaScript.  There are several methods of doing this (Gulp/Grunt tasks, bundlers, etc.) and we'll look at a popular bundler, [Webpack](https://webpack.github.io/).  Webpack also provides some useful services such as script aggregation and injection which we can use to our advantage.

## Setup

First step is setting up npm for the project and pulling in React and Webpack along with the dependencies they'll need for transpilation:
```bash
$ npm init
$ npm install --save react react-dom
$ npm install --save-dev babel-core babel-loader babel-preset-react html-webpack-plugin webpack
```

Since Webpack will be creating our output, we'll need separate directories for the working code and the output code.  I generally name these directories `src` and `dist` but you can pick a naming scheme that works for you.
```bash
$ mkdir src dist
```

## Configuration

With the folder structure set up, create a file named `webpack.config.js` in the root of your project and open it for editing.  Within that file we'll be exporting an object containing configuration data for Webpack:

```javascript
'use strict';

module.exports = {
  entry: [
    './src/app.js'
  ],
  module: {
    loaders: [
      {
        test: /\.js$/,
        loader: 'babel-loader',
        include: `${__dirname}/src`
      }
    ]
  },
  output: {
    filename: 'packed.js',
    path: `${__dirname}/dist`
  }
};
```

To break down what's going on in the configuration object:
- **entry** - an array of files Webpack will be including.  These can be your own files (as shown) with the full path to the files, or they can be Node modules, in which case only the module name is needed.
- **module** - contains the loaders
  - **loaders** - an array of objects, each defining a loader that manipulates specific files or types of files
    - **test** - a regex that defines what files to run the loader on. In this case, all files that end in `.js`
    - **loader** - the module name of the loader to use
    - **include** - directory in which to look for files matching the `test` regex to manipulate. The `__dirname` variable evaluates to the current directory i.e. the root of your project
- **output** - object defining Webpack's output post manipulations
  - **filename** - the name to use for the Webpacked file
  - **path** - the location to output the packed file

We'll also need to configure Babel to know that it's working with React, otherwise it will choke the first time it sees a `<` in our JSX.  In the root of your project create a `.babelrc` file containing the following configuration object:
```javascript
{
  presets: [
    'react'
  ]
}
```

## A Quick Test
To make sure our configuration up to this point is correct, we can write a simple React component and make sure it transpiles properly.  In the `src` directory, create `app.js`:
```javascript
'use strict';

const React = require('react'),
      ReactDOM = require('react-dom'),
      Hello = () => <h1>Hello World!</h1>;

ReactDOM.render(<Hello />, document.getElementById('content'));
```

In the root of your project, run `webpack`, you should see output similar to this:
```bash
$ webpack
Hash: 0c22e9f0817fae5babe0
Version: webpack 1.13.3
Time: 294ms
    Asset     Size  Chunks             Chunk Names
packed.js  1.62 kB       0  [emitted]  main
   [0] multi main 28 bytes {0} [built]
    + 1 hidden modules
```

If you then look in the `dist` folder, there should be a `packed.js` file.  The file contains all of React & ReactDOM (brought in by the `require` statements) along with the simple component we wrote which was transpiled from JSX to JavaScript.

## Script Injection
Since we're already using webpack for transpiling and aggregating our JS, we can take advantage of `html-webpack-plugin` to automatically inject our packed code into our HTML.  First step is installing the npm module:
```bash
$ npm install html-webpack-plugin --save-dev
```

Post-install we include and configure the plugin at the top of the `webpack.config.js` file:
```javascript
const HtmlPlugin = require('html-webpack-plugin'),
      HtmlPluginConfig = new HtmlPlugin({
        template: `${__dirname}/src/index.html`,
        filename: 'index.html',
        inject: 'body'
      });
```
- **template** - the path to the HTML file used as the base for injection.
- **filename** - the name of the file Webpack will output post-injection.
- **inject** - the site in the HTML where the script will be injected.

Post-configuration, we only need to include the plugin in the Webpack pipeline:
```javascript
'use strict';

const HtmlPlugin = require('html-webpack-plugin'),
      HtmlPluginConfig = new HtmlPlugin({
        template: `${__dirname}/src/index.html`,
        filename: 'index.html',
        inject: 'body'
      });

module.exports = {
  entry: [
    'whatwg-fetch',
    './src/app.js'
  ],
  module: {
    loaders: [
      {
        test: /\.js$/,
        loader: 'babel-loader',
        include: `${__dirname}/src`
      }
    ]
  },
  output: {
    filename: 'packed.js',
    path: `${__dirname}/dist`
  },
  plugins: [HtmlPluginConfig]
};
```

With `webpack.config.js` complete, we can check that our Webpack pipeline results in the expected output.  Next step is to add a minimal `index.html` in the `src` directory:
```html
<!DOCTYPE html>

<html>
  <head>
    <title>React &amp; Webpack</title>
  </head>
  <body>
    <div id="content"></div>
  </body>
</html>
```
Now running `webpack` in the root of the project should result in output like this:
```bash
$ webpack
Hash: f4a259286e4068f5a7b7
Version: webpack 1.13.3
Time: 855ms
     Asset       Size  Chunks             Chunk Names
 packed.js     156 kB       0  [emitted]  main
index.html  194 bytes          [emitted]
   [0] multi main 40 bytes {0} [built]
    + 35 hidden modules
Child html-webpack-plugin for "index.html":
        + 3 hidden modules
```
You should end up with a `index.html` and `packed.js` file in your `dist` folder.  Now you can `cd` into `dist`, start up `http-server`, navigate to the served-up page in your browser of choice, and you should see a friendly **Hello World!** message.

Your local React development environment is now configured.  As it can get tedious to run `webpack` after every change to your HTML or React components, you can set Webpack to watch for changes and automatically generate its output on any change to the files it affects with:
```bash
$ webpack --watch
```

## Further Reading
Webpack can do much more than just transpile JSX to JavaScript, such as transpile your Sass to CSS, minify and uglify your code for production, etc.  You can read up more on the various loaders and plugins available for Webpack to incorporate that functionality into your workflow.
- [About Webpack loaders](https://webpack.github.io/docs/loaders.html)
- [List of Webpack loaders](https://webpack.github.io/docs/list-of-loaders.html)
- [About Webpack plugins](https://webpack.github.io/docs/plugins.html)
- [List of Webpack plugins](https://webpack.github.io/docs/list-of-plugins.html)
