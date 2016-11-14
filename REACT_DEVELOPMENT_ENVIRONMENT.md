# React Development Environment

In order for React to be processed by the browser, the JSX needs to be transpiled into regular JavaScript.  There are several methods of doing this (Gulp/Grunt tasks, bundlers, etc.) and we'll look at a popular bundler, [Webpack](https://webpack.github.io/).  Webpack also provides some useful services such as script aggregation and injection which we can use to our advantage.

First step is setting up npm for the project and pulling in React and Webpack along with the dependencies they'll need for transpilation:
```bash
$ npm init
$ npm install --save react react-dom
$ npm install --save-dev babel-core babel-loader babel-preset-react html-webpack-plugin webpack
```
