# Redux Saga
*[Source material](http://yelouafi.github.io/redux-saga/index.html)*

*Saga*'s are implemented using *Generator* functions. To express *Saga* logic we *yield* javascript objects that we call *effects*. These objects contain descriptions that are executed by the *Saga* middleware. *Saga*'s can be thought of as daemons that wait for certain actions to be dispatched to the store.
The *Saga* daemon can be expressed in 3 different ways:
```js
import { take, takeEvery, takeLatest } from 'redux-saga'
import { call, put } from 'redux-saga/effects'

function* mySaga() {
  while(true) {
    let action= yield take('ACTION')
  }
}

function* mySaga() {
  yield* takeEvery('ACTION', function*(action) {  
  })
}

// do not allow concurrent dispatches of ACTION. Any pending ACTION requests
// will be dropped and only latest will be executed (good for throttling?)
function* mySaga() {
  yield* takeLatest('ACTION', function*(action) {  
  })
}
```
- ``take``: Get latest action
- ``takeEvery``: Whenever action occurs, take it and pass to callback. Allows multiple *saga* tasks to be forked concurrently.
- ``takeLatest``: Only get the latest occurrence of action, drop the others, and pass to callback. Cancels any already running task.
- ``put``: Dispatch action to store

### Testing a Saga
Always use `` yield call`` and ``yield put`` to cause effects in *Saga*'s. This makes testing a matter of iterating through the generator and comparing **Effect** descriptors, nothing needs to be mocked since nothing ever gets actually called if we don't actually evaluate the *effects*.

```js
// example Saga to test
function* mySaga() {
  yield* takeEvery('ACTION', action => {  
    let one= yield call(someFunction1, 10)
    let two= yield call(someFunction2, 'a')
    yield put({type: 'MYSAGA_RESULT', payload: {one, two}})
  })
}

// test
it('Testing mySaga', () => {
  const gen= mySaga()
  let result= gen.next(20)
  // asserting that the result is the correct effect DESCRIPTION
  expect(result.value).to.eql(call(someFunction1, 10))
  result= gen.next('b')
  expect(result.value).to.eql(call(someFunction2, 'a'))
  result= gen.next()
  expect(result.value).to.eql(put{type: 'MYSAGA_RESULT', payload: {one: 10, two: 'b'}}))
})
```

### Declarative effects
- ``call``: Declaratively call a function that returns a promise or other generator functions.
```js
yield call(fn, arg1, arg2, ...)
```
- ``apply``: Declaratively invocate a method that returns a promise.
```js
yield apply(obj, obj.method, [arg1, arg2, ...])
```
- ``cps``: *Continuation Passing Style*. Declaratively call a *Node* style function where the ``callback`` is of the form ``(error, result) => ()``. Also supports method invocation for *Node* style methods.
```js
yield cps(fn, arg1, arg2, ...)
yield cps([obj, obj.method], arg1, arg2, ...)
```

### Error handling
Use ``try {} catch() {}`` blocks to handle both thrown errors and rejected promises in ``yield`` statements inside the generator. To test the error handling code path, use the ``throw()`` statement. If the statement we are ``yield``'ing does not throw an error but simply returns an error, the ``yield`` statement will return the error to the generator.
### Common workflows with Saga's
#### Action logger
```js
function* watchAndLog() {
  while(true) { // or yield* takeEvery('*', function* (action) {
    const action = yield take('*')
    console.log(action)
  })
}
```
#### Log in/log out
```js
import { isCancelError } from 'redux-saga'
import { take, put, call, fork, cancel } from 'redux-saga/effects'

function* loginFlow() {
  while(true) {
    const { user, password } = yield take('LOGIN_REQUEST')
    const task = yield fork(authorize, user, password)
    yield take('LOGOUT')
    yield cancel(task)
    yield call(Api.clearItem('token'))
    yield put({type: 'CLEAR_STATE'})
  }
}

function* authorize(user, password) {
  try {
    const token = yield call(Api.authorize, user, password)
    yield put({type: 'LOGIN_SUCCESS', token})
    yield call(Api.storeItem({token}))
  } catch(error) {
    // error instanceof SagaCancellationException
    if(isCancelError(error)) {
      yield put({type: 'LOGIN_CANCELED'})
    } else {
      yield put({type: 'LOGIN_ERROR', error})
    }
  }
}
```
- ``fork``: Fork a concurrent generator. Returns a [*task* object](http://yelouafi.github.io/redux-saga/docs/api/index.html#task).
- ``cancel``: Cancel a generator *task*. Generates a ``SagaCancellationException`` that the generator can catch, but the exception will not bubble up the stack. ``yield cancel(task)`` behaves like a ``fork``, i.e. it will not wait for tasks executing their cleanup code before continuing.

#### Running tasks in parallel
Runs all tasks or *saga*'s in parallel and blocks until all have finished. If one task/*saga* throws an error, cancels all running and returns throws a error.
```js
function* mySaga() {
  try {
    // same as Promise.all
    const [users, repose]= yield [
      call(fetch, '/users'),
      call(fetch, '/repos')
    ]
  } catch(error) {}
}
```
#### Running tasks in a race
Runs all tasks or *saga*'s in a race and blocks until first one finishes. If a error occurs before first one finishes, throws an error.
```js
import { race, take, put } from 'redux-saga/effects'

// execute a network request with a timeout
function* fetchPostsWithTimeout() {
  try {
    const { posts, timeout } = yield race({
      posts   : call(fetchApi, '/posts'),
      timeout : call(delay, 1000)
    })

    if(posts)
      put({type: 'POSTS_RECEIVED', posts})
    else
      put({type: 'TIMEOUT_ERROR'})
  } catch(error) {}
}

// execute a background task but provide a a cancel button
function* backgroundTask() {
  while(true) { ... }
}

function* watchStartBackgroundTask() {
  while(true) {
    yield take('START_BACKGROUND_TASK')
    yield race({
      task: call(backgroundTask),
      cancel: take('CANCEL_TASK') // throws a cancelation error within backgroundTask
    })
  }
}
```
- ``race``: Runs all generators in parallel and waits for the first one to finish. Automatically cancels the tasks that lost the race.
