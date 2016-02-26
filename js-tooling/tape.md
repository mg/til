# Testing with Tape
[Source](https://ponyfoo.com/articles/testing-javascript-modules-with-tape)

[Tape](https://github.com/substack/tape) is a modular testing library that, unlike *mocha* and *jasine*, does not populate the namespace with global functions to execute and manage tests. Tape produces [TAP](https://testanything.org/) output.

```js
import test from 'tape'
import myFunction from './myfunction'

test('testing my synchronous function', t => {
  t.doesNotThrow(() => myFunction())
  t.end()
})

test('testing my asynchronous function', t => {
  myFunction((err, result) => {
    t.equal(err, null)
    t.equal(result, 1)
    t.end()
  })
})
```
- ``t.plan(n)``: Alternative to using ``t.end()``. If the test does not execute *n* tests the test runner will throw an error. Useful as a *guard clause* for our tests.
- ``test.skip()``: Quickly turn of ``test`` statements by changing them into ``test.skip``.

To run *tape* tests, just run *node*. To run tests in a browser, use [testling](https://github.com/substack/testling).
```
node test/*.js
```
