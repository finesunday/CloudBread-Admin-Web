# Development Guideline
This document explains how you can

*You don't need to follow all of this guideline!
I just suggest what I experienced.
If you have better way?
Please share the idea and make people stop suffering!*

## Webpack Configurations
Webpack configuration file is at `./tools/config.js`.

### Dev Port
Port duplication is usual in development.
You can bypass this situation through editing `DEV_PORT` variable.

### Relative Path
Default resolve path is `./src`.
So you can ref some files with relative path even if the file depth is too deep.

### Webpack Loaders
[About webpack loader](http://webpack.github.io/docs/using-loaders.html)
> Loaders are transformations that are applied on a resource file of your app. They are functions (running in node.js) that take the source of a resource file as the parameter and return the new source.
>
> For example, you can use loaders to tell webpack to load CoffeeScript or JSX.

This react package uses below loaders:

* [babel-loader](https://github.com/babel/babel-loader)
* [json-loader](https://github.com/webpack/json-loader)
* [style-loader](https://github.com/webpack/style-loader)
* [css-loader](https://github.com/webpack/css-loader)
* [postcss-loader](https://github.com/postcss/postcss-loader)
* [raw-loader](https://github.com/webpack/raw-loader)
* [url-loader](https://github.com/webpack/url-loader)
* [file-loader](https://github.com/webpack/url-loader)

### Environment Variables
```js
// client and server
if (__DEV__) {
  // this is in development mode
} else {
  // this is in production mode
}

// or you can. but not support for client
if (process.env.NODE_ENV === 'development') {
  // this is in development mode
} else {
  // this is in production mode
}

console.log(__PORT__); // server port
```

## BabelJS Configurations
> [Babel](http://babeljs.io/) is a JavaScript compiler.
> Use next generation JavaScript, today.
> [check out the Babel Handbook!](https://github.com/thejameskyle/babel-handbook)

This react package uses
[ES2015 preset](http://babeljs.io/docs/plugins/preset-es2015/) +
[React preset(JSX)](http://babeljs.io/docs/plugins/preset-react/) +
[Stage 0](http://babeljs.io/docs/plugins/preset-stage-0/) +
[Stage 1](http://babeljs.io/docs/plugins/preset-stage-1/) +
[Stage 2](http://babeljs.io/docs/plugins/preset-stage-2/) +
[Stage 3](http://babeljs.io/docs/plugins/preset-stage-3/)

Checkout `./package.json` and `./tools/config.js` to modify babel configurations.

## Entry Point
Entry file means start point of the part.
You can add more the entry to fill your desire.

### Server
* Server entry file: `./src/server.jsx`.
* Static folder: `./src/public`
* HTML page template: `./src/components/Html/Html.jsx`

Our HTTP server is implemented by [Express](http://expressjs.com/).
You can integrate other HTTP server frameworks such as
[Koa](https://www.npmjs.com/package/koa) and
[Hapi](https://www.npmjs.com/package/hapi), etc...
Place all your public static files to static folder.

#### Add 3rd-party framework
If you want to add venders (such as jQuery, Bootstrap, etc...) to your document,
edit HTML page template and place your venders to static folder.
[React Helmet](#document-configuration) is also good solution.

**Be careful** It can make your application very dirty, I don't recommend.

### Client
* Client entry file: `./src/client.jsx`.

## Core Utility
I brought some useful utility placed `./src/core`.

### Redux Store
```js
import { getStore } from 'core/context';

const store = getStore();
store.dispatch({
  type: 'SOME_ACTION_CONSTANT',
});

console.log(store.getState());
```

If you need to authenticate or bring api end point for your data fetcher,
You can get store's state, like:
```js
const store = getStore();
const state = store.getState();
const { user, config } = state;
const { accessToken } = user;
const { apiEndPoint } = config;
```

### Check Client Environments
```js
import {
  canUseDOM,
  canUseWorkers,
  canUseEventListeners,
  canUseViewport,
} from 'core/env';

if (canUseDOM) {
  // it's client ...
}
if (canUseWorkers) {
  // it's client and support workers ...
}
if (canUseEventListeners) {
  // it's client and support event listener ...
}
if (canUseViewport) {
  // it's client and support viewport ...
}
```
You can set environment variable such as `__CLIENT__` or `__SERVER__` in Webpack Config.

### Data Fetcher
It uses [superagent](https://www.npmjs.com/package/superagent) module to fetch data.
You can choose other universal http request client like [isomorphic-fetch](https://www.npmjs.com/package/isomorphic-fetch).

#### Basic Usage
```js
import fetch from 'core/fetch';

// find a user
async function getUserById(id) {
  const res = await fetch.get(`/users/${id}`);
  return res.body;
}

// update a user
async function updateUserById(id, data) {
  const res = await fetch.patch(`/users/${id}`, { data });
  return res.body;
}

// delete a user
async function deleteUserById(id, data) {
  const res = await fetch.del(`/users/${id})`);
  return res.body;
}
```

## Expand API

### Express middleware
In `./src/api` directory, you can add some api routes.

```js
// something.js
import { Router } from 'express';

const router = Router();

router
  .route('/users/:userId')
  .get((req, res) => { ... }))
  .patch((req, res) => { ... })
  .delete((req, res) => { ... }));

export default router;
```

```js
// In api.js
// you can bind like this
router.use(require('something').default);
```

[Here are more examples](http://expressjs.com/en/guide/routing.html)

### GraphQL
GraphQL directory is `./src/data` and `schema.js` is in the path.
You can customize Queries and Mutations.

## React Guideline
There are three folders to manage react components.
* `./src/components`: common components
* `./src/containers`: page components
* `./src/layouts`: layout components

### Trouble Shooting Tool

### Document Configuration
If you need one of among below:

* change document title
* add some script files
* add some link tags
* add some meta tags

The answer is [react-helmet](https://github.com/nfl/react-helmet#use-cases).

### Route Configurations
Routes entry file is at `./src/routes/routes.jsx`.
To modify routes, you need to read [react-router reference](https://github.com/reactjs/react-router/tree/master/docs)

### React Component

I recommend you to compose your react component this way.

* Navigation
  * Navigation.jsx(or can be es6 and js, etc...)
  * Navigation.scss
  * package.json

```js
import React from 'react';
import styles from './Navigation.scss';

function Navigation() {
  return (
    <div className={styles.Container}>
      <ul className={styles.Navigation}>
        <li>nav item</li>
        <li>nav item</li>
        <li>nav item</li>
      </ul>
    </div>
  );
}

export default Navigation;
```

```css
.Container {
  position: fixed;
  top: 0; left: 0; right: 0;
  height: 56px;
}

.Navigation {
  display: block;

  > li {
    display: inline-block;
  }
}
```

```json
{
  "private": true,
  "main": "./Navigation.jsx"
}
```

Curious at css flow? read [this](#css-guideline)

#### Pure Component (Stateless Component)
```js
import React from 'react';

function PureComponent(...props) {
  return (
    <div>Render contents</div>
  );
}

PureComponent.propTypes = { ... };
PureComponent.defaultProps = { ... };
PureComponent.contextTypes = { ... };
```

*HMR doesn't support pure component.*

#### State Component
```js
import React, { Component } from 'react';

class StateComponent extends Component {
  static propTypes = { ... };
  static defaultProps = { ... };
  static contextTypes = { ... };

  componentWillMount() {
    // ...will appear
  }

  componentDidMount() {
    // ...ready
  }

  componentWillUnmount() {
    // ...will disappear
  }

  render() {
    return (
      <div>Render contents.</div>
    );
  }
}
```
If you don't need state or lifecycle callback,
pure component is better.
It makes your application very light.

## Redux Guideline
* `./src/actions`: action creators
* `./src/constants`: action constants
* `./src/reducers`: reducers
* `./src/redux`: store creator and reducer creator

Our store has `redux-thunk` middleware.
In development, `redux-logger` is included.
You can add middlewares. See `./src/redux/middlewares`.

### Basic Usage

```js
// ./src/constants/some.js
export const SOME_ACTION_CONSTANT = 'SOME_ACTION_CONSTANT';
```

```js
// ./src/actions/some.js
import { SOME_ACTION_CONSTANT } from 'constants/some';

export function someAction() {
  return {
    type: SOME_ACTION_CONSTANT,
    // you can pass some arguments
    payload: { ... },
    data: { ... },
  };
}
```

```js
// ./src/reducers/some.js
const initialState = {
  count: 0,
};
export default function reducer(state = initialState, action = {}) {
  switch (action.type) {
    case SOME_ACTION_CONSTANT: {
      const { ... } = action.payload; // you can receive some arguments.
      return {
        ...state,
        count: state.count + 1,
      };
    }
    default: {
      return state;
    }
  }
}
```

**Be careful** you have to add your reducer to index file.

```js
// ./src/reducers/index.js
export some from './some';
```

```js
// ./src/components/SomeComponent/SomeComponent.jsx
import React, { PropTypes } from 'react';
import { connect } from 'react-redux';
import { bindActionCreators } from 'redux';
import { someAction } from 'actions/some';

function mapStateToProps(state) {
  return {
    count: state.some.count,
  };
}

function mapDispatchToProps(dispatch) {
  return bindActionCreators({
    someAction,
  }, dispatch);
}

function SomeComponent({ count, someAction }) {
  return (
    <div>
      <p>I am pure component. I did some action {count} times!</p>
      <button onClick={someAction}>Some Action</button>
    </div>
  );
}

SomeComponent.propTypes = {
  count: PropTypes.number,
  someAction: PropTypes.func,
};

export default connect(mapStateToProps, mapDispatchToProps)(SomeComponent);
```

### Server side prefetch data
Some components need to fetch data before serving to clients.
You can set `needs` property to pre-fetch data.

```js
SomeComponent.needs = [
  someAction,
];
```

Then server will render components after dispatching someAction.
It's important to synchro client and server.
The server will provide `__SYNC_DATA` global variable to javascript.
Redux initializes with `__SYNC_DATA`.

## CSS Guideline

You can insert CSS through three ways.

### Public stylesheet
Place your stylesheet to `./src/public` folder and reference stylesheet
through [React Helmet](#document-configuration)

### Compiled stylesheet
Our style-loader, css-loader and postcss-loader solve this!

```scss
/* ./src/components/SomeComponent/SomeComponent.scss */

/* you can use postcss */
$color: #fff;
$width: calc(1000px - 20px);

.SomeClass {
  display: flex;
  color: $color;
  width: $width;

  .NestedClass {
    color: Container from "styles/container"; /* See postcss documents */
  }
}
```

```js
// ./src/components/SomeComponent/SomeComponent.js
import React from 'react';
import styles from './SomeComponent.scss';

export default SomeComponent() {
  return (
    <div className={styles.SomeClass}>
      Some Component!
    </div>
  );
}
```

You can consider [classnames](https://www.npmjs.com/package/classnames) module to activate or inactivate some class.

### Inline stylesheet
* [Offical Inline CSS](https://facebook.github.io/react/tips/inline-styles.html)
* [React InlineCSS Component](https://github.com/RickWong/react-inline-css)