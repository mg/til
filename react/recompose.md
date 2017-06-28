# Recompose

- ``compose``: **Compose** many **HOC**'s into one **HOC**

```js
const enhance = compose(
  hoc1,
  hoc2,
)

const MyComponet1 = enhance(({}) => <div/>)
const MyComponet2 = hoc2(hoc1(
  ({}) => <div/>
))
```

- ``setDisplayName(NAME)``: **HOC** to set *display name* of component.

- ``setPropTypes(PROPTYPES_OBJECT)``: **HOC** to describe components *prop types*.

- ``withState(STATE_NAME, SET_STATE_FUNCTION, INITIAL_VALUE)``: **HOC** to add *state* to a component.

```js
const MyComponent = withState('myState', 'onChangeMyState', 0)(
  ({myState, onChangeMyState}) =>
    <div onClick={() => onChangeMyState(2)}>{myState}</div>
)
```

- ``withHandlers()``: set up *handlers* on component. Each *handler* accepts *owner props* and returns the *handler function*.

```js
const MyComponent = withHandlers({
  myHandler1: ({prop1, someFn}) => someFn(prop1),
  myHandler2: ({prop1, someFn}) => someFn(!prop1),
})(
  ({myHandler1, myHandler2}) => <div/>
)
```

- ``withReducer(STATE_NAME, DISPATCH_NAME, REDUCER, INITIAL_VALUE)``: Similar to ``withState`` it adds state to a component, but using **Redux** semantics.

```js
const MyComponent = compose(
  withReducer(
    'myState',
    'myDispatch',
    (state, action) => {
      switch(action.type) {
        case 'ON': return 'on'
        case 'OFF': return 'off'
        default: return state
      }
    },
    'on',
  ),
  withHandlers({
    setOn: ({ myDispatch }) => () => myDispatch({type: 'ON'}),
    setOff: ({ myDispatch }) => () => myDispatch({type: 'OFF'}),
  })
)(
  ({setOn, setOff, myState}) => <div/>
)
```

- ``lifecycle(LIFECYCLE_OBJECT)``: Enhance any component with *React lifecycle methods*. The component receives any state as a prop.

```js
const MyComponent = lifecycle({
  state: { myState: 0 },
  componentDidMount() {
    this.setState(prev => ({myState: prev.myState + 1}))
  },
})(
  ({myState}) => <div/>
)
```

- ``mapProps(OWNER_PROPS => PROPS)``: **HOC** that accepts a mapping function that **replaces** the *owner props* with a new *prop* object.

- ``withProps(OWNER_PROPS => PROPS)``: **HOC** that accepts a mapping function that **merges** the *owner props* with a new *prop* object.

- ``flattenProp(PROP_NAME)``: Looks for owner prop **PROP_NAME** and spreads it's properties as props for enhanced component. I.e. if component has property A with properties B and C, the enhanced component will have properties B and C.

- ``branch(PREDICATE, IF_TRUE, IF_FALSE?)``: Enhance any component with branching logic. If the predicate is true, execute the **IF_TRUE** branch. If it is false, execute the **IF_FALSE** branch, OR if no **IF_FALSE** branch is provided, return the enhanced component.

- ``renderComponent(COMPONENT)``: Function that renders a component. Used with ``branch`` to specify the **IS_TRUE** and the optional **IS_FALSE** branches.

- ``renderNothing()``: Serves a similar function to ``renderComponent()``. Useful to render nothing when using ``branch()`` to conditionally render a component.

- ``componentFromProp(PROP_NAME)``: Use the value of a prop to decide what component to render.

- ``withPropsOnChange([PROPS_NAMES], OWNER_PROPS => PROPS)``: Execute a mapping function from *owner props* to *props* whenever the value of a prop in **PROP_NAMES** changes. Useful to declare a rendering with *lazy computation*.

- ``pure``: Enhance a component to be *pure*.

- ``onlyUpdateForKeys([PROP_NAMES])``: Enhance a component to be *pure* with respect to **PROP_NAMES**.

- ``onlyUpdateForPropTypes``: Enhance a component to be *pure* with respect to declared *prop types*. Useful in conjunction with ``setPropTypes``.

- ``shouldUpdate((PREV_PROPS, NEXT_PROPS) => BOOLEAN)``: Enhance a component to be *pure** with respect to a provided predicate function that accepts **PREV_PROPS** and **NEXT_PROPS** and returns either **true** or **false**.
