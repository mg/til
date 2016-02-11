# ES6 Generators, part 2
*[Source material](http://www.2ality.com/2015/03/es6-generators.html)*, sections 5 to 10

Multitasking in JS is achieved through
- **WebWorkers**: multiprocessing where data can only be shared by message passing.
- **Cooperative**: Processes voluntarily give up control. Fits well with *async operations* where there are natural pauses (such as fetching a response from a server) and *stream processing* where there are pauses if there is no data available. Achieved in JS mainly with the event loop. Data isolation is achieved with *run to completion*.

### Generators as a Task (coopirative multitasking)
*Generators* are ideal as clients of *Promises*, using [co](https://github.com/tj/co) to create *shallow coroutines*.
``` js
const getFile= url => fetch(url).then(request => request.text())

co(function* () {
  // the task
  try {
    let [croftStr, bondStr] = yield Promise.all([
      getFile('http://localhost:8000/croft.json'),
      getFile('http://localhost:8000/bond.json'),
    ])
    let croftJson = JSON.parse(croftStr)
    let bondJson = JSON.parse(bondStr)

    console.log(croftJson)
    console.log(bondJson)
  } catch (e) {
    console.log('Failure to read: ' + e);
  }
})
```

Generators are *push* based and *lazy*, iterators are *pull* based and *eager*.
**return()** can be used by the loop controller to signal to the generator that the sequence has ended and it should clean up. Allows the generator to run in an infinite loop and outsource the end-of-stream responsibility.

Generator functions are regular JS functions, can be **new**-ed, functions can be added to the prototype, have a special prototype called **Generator.object**.

## Misc
### Generator interface
``` js
interface Generator {
  next(value? : any) : IteratorResult;
  throw(value? : any) : IteratorResult;
  return(value? : any) : IteratorResult;
}

interface IteratorResult {
  value : any;
  done : boolean;
}
```
