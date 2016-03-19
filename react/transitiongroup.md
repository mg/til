# Animation with ReactTransitionGroup
[Source 1](https://medium.com/@cheapsteak/animations-with-reacttransitiongroup-4972ad7da286#.hbzo42n48),
[source 2](https://medium.com/@cheapsteak/reusing-reacttransitiongroup-animations-with-higher-order-components-1e7043451f91#.9sio3r9ex)

For animations written in Javascript
```
npm i --save react-addons-transition-group
```

### Lifecycle hooks
Lifecycle methods that are implemented on the components that are to be animated. Callbacks must be called to finish the animations.
- ``componentWillAppear(cb)``: Only called on the initial rendering of a ``TransitionGroup``. Called on the component ate the same time as the component's ``componentDidMount``. Blocks until ``cb`` is called.
- ``componentDidAppear()``: Called after the ``cb`` passed to ``componentWillAppear`` is called.
- ``componentWillEnter(cb)``: Called on a component that is being added to the transition, at the same time as the component's ``componentDidMount``.
- ``componentDidEnter()``: Called after the ``cb`` passed to ``componentWillEnter`` is called.
- ``componentWillLeave(cb)``: Called after the component has been removed from the ``TransitionGroup``. The component is kept in the DOM until ``cb`` is called.
- ``componentDidLeave()``: Called after the ``cb`` passed to ``componentWillLeave`` is called.

### Example animation
Higher Order Component that decorates an component with enter/leave animation.

```js
function fadeInOutUpFactory(Component, options= {duration: 0.3, distance: 100 }) {
  return class FadesUp extends React.Component {
    componentWillEnter(cb) {
      const el = findDOMNode(this);
      TweenMax.fromTo(el, options.duration, {y: options.distance, opacity: 0}, {y: 0, opacity: 1, onComplete: cb})
    }

    componentWillLeave(cb) {
      const el = findDOMNode(this);
      TweenMax.fromTo(el, options.duration, {y: 0, opacity: 1}, {y: -options.distance, opacity: 0, onComplete: cb})
    }

    render() {
      return <Component {...this.props}/>
    }
  }
}

function fadeInOutUp(Component) {
  return typeof arguments[0] === 'function'
    ? fadeInOutUpFactory(arguments[0])
    : Component => fadeInOutUpFactory(Component, arguments[0])
}
```

Using the animation component
```js
@fadeInOutUp({distance: 10})
const Box= () => <div className="box"/>

const Circle= () => <div className="box"/>
export default fadeInOutUp({duration: 0.8})(Circle)
```
