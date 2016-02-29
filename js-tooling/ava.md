# AVA
[Source](https://github.com/sindresorhus/ava/#)
Concurrent test runner that uses tape.

Configure in ``package.json``
```json
{
  "scripts": {
    "test": "ava"
  },

  "ava": {
    "files": [
      "src/*.spec.js",
      "src/*.spec.jsx",
      "!**/not-this.file.spec.js"
    ],
    "failFast": true,
    "tap": true,
    "require": [
      "babel-register"
    ]
  }
}
```

Accepts simple arguments through *npm* to run specific test files: ``npm test test.js``.

```js
// enable ES2015 in our code
import 'babel-register'

// basic test syntax
test('name', t => {
  t.pass()
})

// test name is optional
test(t => {
  t.pass()
})

// serial test
test.serial(t => {
  t.pass()
})

// only run this test, skip all others
test.only('only test run', t => {
  t.pass()
})

// skip this test
test.skip('will not be run', t => {
  t.fail()
})

// runs before all tests
test.before(async t => {
  await promiseFn()
})

// runs before each test, set context data
test.beforeEach(t => {
  t.context.data= 'DATA'
})

// access shared context data
test(t => {
  console.log(t.context.data) // outputs DATA
})

// runs after each test
test.afterEach(t => {

})

// runs after all tests
test.after(t => {
})

// when test returns a promise, the test will end when the promise resolves
test(t => {
  return myPromise().then(resutl => {
    t.is(result. 'TEST')
  })
})

// supports generators
test(function* (t) {
  const val= yield someFn()
  t.true(value)
})

// supports async functions
test(async t => {
  const value= await someFn()
  t.false(value)
})

// supports oveservables
test(t => {
  return Observable.of(1, 2, 3, 4)
    .filter(n => n % 2)
    .map(() => t.pass())
})

// async test using a callback, must end with t.end()
test.cb(t => {
  t.plan(1)
  someAsyncFunction(() => {
    t.pass()
    t.end()
  })
})
```

Assertions
- ``.pass([message])``
- ``.fail(['message'])``
- ``.ok(value, [message])``
- ``.notOk(value, [message])``
- ``.true(value, [message])``
- ``.false(value, [message]()``
- ``.is(value, expected, [message])``
- ``.not(value, expected, [message])``
- ``.same(value, expected, [message])``
- ``.notSame(value, expected, [message])``
- ``.throws(function|promise, [error, [message]])``
- ``.notThrows(function|promise, [message])``
- ``.regex(contents, regex, [message])``
- ``.ifError(error, [message])``

Use [nyc](https://github.com/bcoe/nyc) for code coverage.
