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
    - **include** - directory in which to look for files matching the `test` regex to manipulate. the `__dirname` variable evaluates to the current directory i.e. the root of your project
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

const React = require('react');

function Hello() {
  return(<h1>Hello World!</h1>);
}
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

If you then look in the `dist` folder, there should be a `packed.js` file.  The file itself is all of React (brought in by the `require('react')` statement) along with the simple component we wrote which was transpiled from JSX to JavaScript.
