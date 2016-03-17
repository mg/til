# Performance Tools
[Source 1](https://facebook.github.io/react/docs/perf.html),
[source 2](http://benchling.engineering/performance-engineering-with-react/)
[source 3](http://benchling.engineering/deep-dive-react-perf-debugging/)
```
npm i --save-dev react-addons-perf
```
### Methods
- ``Perf.start()``: Start a measurement
- ``Perf.stop()``: Stop a measurement
- ``Perf.getLastMeasurements()``: Get last measurements
- ``Perf.printInclusive(measurements)``: Print overall time. Defaults to all measurements from last recording if passed undefined.
- ``Perf.printExclusive(measurements)``: Doesn't include time taken to mount components: processing props, ``getInitialState``, ``componentWillMount``, ``componentDidMount``, etc.
- ``Perf.printWasted(measurements)``: Wasted time spent in components that did not result in any rendering. Indicates lots of unnecessary VDOM manipulations that could be avoided with ``shouldComponentUpdate``.
- ``Perf.printDOM(measurements)``: Print underlying DOM manipulations.

The results from ``Perf.getLastMeasurements()`` looks like
```js
{
  "exclusive": {},
  // '.0.0' is the React ID of the node
  "inclusive": {".0.0": 0.0670000008540228, ".0": 0.3259999939473346},
  "render": {".0": 0.036999990697950125, ".0.0": 0.010000003385357559},
  // Number of instances
  "counts": {".0": 1, ".0.0": 1},
  // DOM touches
  "writes": {},
  // Extra debugging info
  "displayNames": {
    ".0": {"current": "App", "owner": "<root>"},
    ".0.0": {"current": "Box", "owner": "App"}
  },
  "totalTime": 0.48499999684281647
}
```
