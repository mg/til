# Async / Await
*[Source material](https://ponyfoo.com/articles/understanding-javascript-async-await)*

**await** can only be used in an **async** function, and every **async** function *always* returns a promise.
``` js
async function f() {
  // use try catch to handle errors in funcIReturnAPromise() here
  // we block here
  let ret= await funcIReturnAPromise()
  // ret is the value resolved, not the promise; the promise is implicit

  // wait for all promises to resolve
  let [r1, r2]= await Promise.all([p1, p2])

  // wait for the first to reslove
  let rr= await Promise.race([p1, p2])

  // available to caller in then()
  return {ret, r1, r2, rr}
}

// if we don't handle promise error in f(), we can catch it here
f.then(res => {}).catch(e => {})
```

**async** function is akin to wrap a *generator* inside a *promise* and step through the generator inside the *promise*.
