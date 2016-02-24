# Reselect
Selectors for Redux
- Selectors can compute derived data, allowing Redux to store the minimal possible state.
- Selectors are not recomputed unless one of its arguments change.
- Selectors are composable.
- The memoizer has a cache size of 1 and uses referential equality to check for changes, is optimized for immutable datatypes.
- Replaces the ``mapStateToProps``function when calling Redux ``connect()``.

Selectors are created with the ``createSelector`` function that has the signature ``createSelector(...inputSelectors | [inputSelectors], resultFunc)``.
```js
import { createSelector } from 'reselect'

// simple selectors, not memoized
const selectorA= state => state.a
// read value from component props rather than Redux store
const selectorB= (state, props) => props.b

// my memoized selector
const mySelector= createSelector(
  selectorA,
  selectorB,
  (a,b) => a + b
)

const selectorC= state => state.c

// compose mySelector into mySecondSelector
const mySecondSelector= createSelector(
  selectorC,
  mySelector,
  (c, ab) => c * ab
)

// Connecting to Redux and a React component
export default connect(
  mySecondSelector,
  mapDispatchToProps,
)(MyComponent)
```

The ``createStructuredSelector`` is a convenience function to easily map selectors to properties, has the signature ``createStructuredSelector({inputSelectors}, createSelector= createSelector)``. Structured selectors can be nested.
```js
const mySelectorA= state => state.a
const mySelectorB= state => state.b

const structuredSelector= createStructuredSelector({
  x: mySelectorA,
  y: mySelectorB,
})

const reslult= structuredSelector({a: 5, b: 6}) // => {x: 5, y:6}
```

If the default memoize function is not good enough, we can create a custom ``createSelector`` function with the ``createSelectorCreator`` function.
