# `conventional-component` [![Build Status](https://travis-ci.org/sebinsua/conventional-component.png)](https://travis-ci.org/sebinsua/conventional-component) [![npm version](https://badge.fury.io/js/conventional-component.svg)](https://www.npmjs.com/package/conventional-component)
> 🏴 React components which can have their state hoisted into Redux.

As I search for components with which to build an application, I frequently find otherwise excellent components which have inaccessible state. Often this means complications when integrating with Redux, due to how the state is passed in and emitted. Sometimes I will try to find a `react-redux-` variant of a component, however these unfortunately lose the ease of integration of plain React components.

This is a proposal to build components out of reducers and actions and a library to help do so. The intention is to make it easy to write standardised components which (1) can be quickly installed into an app, and (2) can have their state hoisted into Redux if the rest of the app needs to consume it.

It's loosely inspired from the conventions within [`erikras/ducks-modular-redux`](https://github.com/erikras/ducks-modular-redux).

## Convention

```js
export {
  actions,
  reducer,
  withLogic,
  Template,
  REDUCER_NAME,
  COMPONENT_NAME,
  COMPONENT_KEY
}
export default Component
```

### Rules

A `Component`...

1. **MUST** `export default` itself.
    1. **MUST** store its state using `connectToState(reducer, actions)`.
    2. **MUST** dispatch an `init(identity, props)` action on either construction or `componentWillMount`.
    3. **MUST** `export` the name of the component as `COMPONENT_NAME`.
    4. **MUST** `export` the primary key of each of the components as `COMPONENT_KEY` (e.g. `id`, `name`).
2. **MUST** `export` its action creator functions as `actions`.
    1. **MUST** either wrap each of its actions with `withActionIdentity(actionCreator)` or use action creators with the same signature.
3. **MUST** `export` its reducer as `reducer(state, action)`.
    1. **MAY** `export` the default name for its reducer as `REDUCER_NAME`.
4. **MUST** `export` its component logic as a higher-order component (HOC) `withLogic(Template)`.
5. **MUST** `export` its component template as `Template`.

## Example

### Component

The best way to understand the convention is to read [some example code for an `Input` component](./example/src/Input).

```js
import Input, { COMPONENT_NAME, COMPONENT_KEY } from './Input'
import Template from './InputDisplay'
import withLogic from './withLogic'
import reducer, { REDUCER_NAME } from './reducer'
import * as actions from './actions'

export {
  actions,
  reducer,
  withLogic,
  Template,
  REDUCER_NAME,
  COMPONENT_NAME,
  COMPONENT_KEY
}
export default Input
```

### Redux

The best way to understand how the state can be hoisted into Redux is to read [some example code in which this is done (`ReduxTest`)](./example/src/Redux).

#### Component

```js
import { asConnectedComponent } from 'conventional-component'

import {
  actions,
  withLogic,
  Template,
  REDUCER_NAME,
  COMPONENT_NAME,
  COMPONENT_KEY
} from '../../Input'

export default asConnectedComponent({
  actions,
  withLogic,
  Template,
  REDUCER_NAME,
  COMPONENT_NAME,
  COMPONENT_KEY
})
```

#### Reducer

```js
import { withReducerIdentity } from 'conventional-component'

import { reducer, COMPONENT_NAME, REDUCER_NAME } from '../../Input'

export { REDUCER_NAME }
export default withReducerIdentity(COMPONENT_NAME, reducer)
```

## API

### Component

#### `connectToState(reducer, actions)`

#### `init(identity, props)`

#### `withActionIdentity(actionCreator)`

#### `receiveChildrenAsFunction(props)`

### Redux

#### `asConnectedComponent(conventionalComponentConfiguration)`

###### `createIdentifier(componentName, componentKey)`

###### `bindIdentityToActionCreators(identifier, actionCreators) => identifiedActions(props): ActionCreators`

###### `createMapStateToProps(reducerName, identifier, structuredSelector)`

#### `withReducerIdentity(identifierPredicate, identifiedReducer)`

## Install

```sh
yarn add conventional-component
```