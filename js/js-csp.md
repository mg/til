# js-csp
[Source](http://jlongster.com/Taming-the-Asynchronous-Beast-with-CSP-in-JavaScript)
[Library](https://github.com/ubolonton/js-csp)

Channels are useful for coordinating concurrent tasks that run at the same time or asynchronously. Provide a model for exchanging data between tasks with message passing.

```js
let ch= chan()

go(function*() {
  let val
  while((val= yield take(ch)) !== csp.CLOSED) {
    console.log(val)
  }
})

go(function*() {
  yield put(ch, 1)
  yield take(timeout(1000))
  yield put(ch, 2)
  ch.close()
})
```
- ``chan()`` creates a new channels. For bufferning use ``chan(n)``.
- ``go`` block returns a channel that holds the final value from the process.
- ``put`` returns false if channel is closed (``while(yield put(ch, val))``).
- ``take`` returns ``csp.CLOSED`` if channel is closed (``while((v= yield take(ch)) !== csp.CLOSED)``).
- ``timeout(ms)`` returns a channel that closes after specified time
### Basic Principles of CSP
- Processes are spawned with ``go``, and channels are created with ``chan``. Processes communicate by sending messages trough channels.
- Use ``take`` and ``put`` to operate on channels within a process. Both operations block.
### Using channels for network requests (replacing the promise)

```js
// url => channel that yields content of url or error
function httpRequest(url) {
  let ch= chan()
  let req= new XMLHttpRequest()
  req.onload= function() {
    if(req.status === 200) {
      putAsync(ch, this.responseText)
    }
    else {
      putAsync(ch, new Error(this.responseText))
    }
  }
  req.open('get', url, true)
  req.send()
  return ch
}

// url => channel that yields js object from JSON or error
function jsonRequest(url) {
  return go(function*() {
    let value= yield take(httpRequest(url))
    if(!(value instanceof Error)) {
      value= JSON.parse(value)
    }
    return value // value is put on channel that go block returns
  });
}

go(function*() {
  // data is a js object
  var data= yield take(jsonRequest('sample.json'))
  console.log(JSON.stringify(data))
});
```
- ``putAsync``: put a value on a channel outside a ``go`` block. Accepts a callback when done. ``takeAsync``is also available.
### UI programming with channels

```js
// (DOM, event) => channel with event stream
const listen= (el, type) => {
  let ch= chan()
  el.addEventListener(type, e => putAsync(ch, e))
  return ch
}

go(function*() {
  // dom element to watch for both mouse moves and clicks
  let el= document.querySelector('#ui2')
  let mousech= listen(el, 'mousemove')
  let clickch= listen(el, 'click')

  while(true) {
    // wait for both channels, release when either offers a value
    let v = yield alts([mousech, clickch])
    let event = v.value;
    if(v.channel === mousech) {
      // event is from mousech
    } else {
      // event is from clickch
    }
  }
})
```
- ``alts`` blocks on n channels and gets the next value from any of them. Similar to *race* when using promises. Has an alternative from to perform *put*s as well as *take*s ``alts([ch1, ch2, [ch3, 5]])``.  

### Buffering
- ``chan(5)``: creates a *normal* buffer that blocks when full.
- ``csp.buffers.dropping(5)``: creates a *dropping* buffer that never blocks but drops new elements if buffer is full.
- ``csp.buffers.sliding(5)``: creates a *sliding* buffer that never blocks but drops oldest element in buffer if buffer is full.
Buffering helps with implementing strategies such as *backpressure*.
