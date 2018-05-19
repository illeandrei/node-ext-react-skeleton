# `Note:` missing proxy config in ./client/package.json
`"proxy": "http://localhost:3001/",`



# `create-react-app` with a server example

 [![Dolpins](https://cdn.rawgit.com/fullstackio/cq/master/doc/readme/dolphins-badge-ff00ff.svg)](https://www.fullstackreact.com)

This project demonstrates using the setup generated by `create-react-app` alongside a Node Express API server.

We have a [detailed blog post](https://www.fullstackreact.com/articles/using-create-react-app-with-a-server/) that explains this repository.

## Running

```
git clone git@github.com:fullstackreact/food-lookup-demo.git
cd food-lookup-demo
npm i

cd client
npm i

cd ..
npm start
```

## Overview

`create-react-app` configures a Webpack development server to run on `localhost:3000`. This development server will bundle all static assets located under `client/src/`. All requests to `localhost:3000` will serve `client/index.html` which will include Webpack's `bundle.js`.

In this example, the React component `App` makes a request to an API server (`server.js`). The user's browser visits the Webpack dev server at `localhost:3000`. Then the React client communicates with the API server when needed at `localhost:3001`:

![Flow diagram](./flow-diagram.png)

This setup uses [node-foreman](https://github.com/strongloop/node-foreman) for process management. Executing `npm start` instructs Foreman to boot both the Webpack dev server and the API server.



## ext-react-cra-eject

This project is a React app ejected from create-react-app v1.3.3 that has been modified to use ExtReact.  All of the changes needed to get ExtReact working after ejecting from create-react-app are in [this diff](https://github.com/sencha/ext-react-cra-eject/compare/after-eject...with-ext-react).

## Tags

This repo provides tags to help you understand the changes that need to be made to the ejected react app in order
to integrate ExtReact:

### before-eject

The state of the app just after initial creation, before ejecting.

### after-eject

The state of the app directly after ejecting, before adding ExtReact.

## Setup

In order to install the ExtReact npm packages, you need to authenticate to Sencha's npm registry.  You can get credentials by
[signing up for a trial of ExtReact](https://www.sencha.com/products/extreact/evaluate/).  Once you receive your credentials from Sencha, run the following:

```
npm login --registry=https://npm.sencha.com --scope=@extjs
```

## Running

```
npm install
npm start
```

You can view the app at http://localhost:3000

## Steps to add ExtReact after ejecting from create-react-app

### 1. Install packages from Sencha

```
npm i --save @extjs/reactor @extjs/ext-react @extjs/reactor-webpack-plugin @extjs/reactor-babel-plugin
```

### 2. Configure Babel

In package.json, change the babel configuration to:

```json
"babel": {
  "presets": [
    "react-app"
  ],
  "plugins": [
    "@extjs/reactor-babel-plugin"
  ]
}
```

### 3. Add reactor-webpack-plugin to webpack config files

#### config/webpack.config.dev.js

Add the following to the top of config/webpack.config.dev.js:

```JavaScript
const ExtReactWebpackPlugin = require('@extjs/reactor-webpack-plugin');
``` 

Then, add the following as the first entry under `plugins`:

```JavaScript
  new ExtReactWebpackPlugin(),
```

#### config/webpack.config.prod.js

Add the following to the top of config/webpack.config.prod.js:

```JavaScript
const ExtReactWebpackPlugin = require('@extjs/reactor-webpack-plugin');
``` 

Then, add the following as the first entry under `plugins`:

```JavaScript
  new ExtReactWebpackPlugin({
    production: true
  }),
```

### 4. Alter webpack dev server config

In webpackDevServer.config.js, change the `contentBase` value to:

```JavaScript
contentBase: [paths.appPublic, paths.appBuild],
```

### 5. Use reactor's `launch` function

In, src/index.js replace:

```JavaScript
import ReactDOM from 'react-dom';
```

with:

```JavaScript
import { launch } from '@extjs/reactor';
```

and replace

```JavaScript
ReactDOM.render(<App />, document.getElementById('root'))
```

with:

```JavaScript
launch(<App />);
```

### 6. Remove the root div from index.html

Since reactor's launch function makes it's own viewport div, you can remove this line from src/index.html:

```HTML
<div id="root"></div>
```

### 7. Add .ext-reactrc to the root of your project

Since create-react-app uses separate webpack config files for development and production, we recommend putting
shared config options for ExtReactWebpackPlugin in a `.ext-reactrc` file in the root of your project.  For example, the following
Sets the output path for the ExtReact bundle to static/js/ext-react, to match the default output path set by create-react-app.

```json
{
    "output": "static/js/ext-react"
}
```

### 8. Add Ext as a global to the ESLint config

In package.json, add Ext as a global by changing the eslintConfig to:

```
"eslintConfig": {
  "extends": "react-app",
  "globals": {
    "Ext": true
  }
}
```  

That should be all that's required.  Once you've completed these steps, you can import components from ExtReact as seen here:

https://github.com/sencha/ext-react-cra-eject/blob/master/src/App.js#L4
