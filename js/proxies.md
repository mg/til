# Proxies
*[Source material](https://developers.google.com/web/updates/2016/02/es2015-proxies)*

A *proxy* is an object that modifies the behavior of the *target* with the functions **get()**, **set()** or **apply()**. Any function target that is *not* handled in proxy gets forwarded to *target*. Target can be an object with properties or a function object.
``` js
let target = { /* some properties */ }
let proxy = new Proxy(target, {
  get: function(target, name, receiver) {
    // just forward it
    return target[name]
  },

  set: function(target, name, value) {
    // just forward it
    target[name]= value
  }
})

proxy.test= 'test'
console.log(proxy.test)

function sum(a, b) {
  return a + b
}

let fproxy= new Proxy(sum, {
  apply: function(target, thisArg, ags) {
    // just forward it
    return target.apply(thisArg, args)
  }
})

fproxy(1, 2)
```

Use cases for proxies can include
- access control
- provide access to an object that resides in a different address space (e.g. on the server or in a web worker)
