# Webpack Codesplitting
[Source](https://medium.com/@ryanflorence/welcome-to-future-of-web-application-delivery-9750b7564d9f#.hnmlxg9kh)

Dynamic loading in Webpack revolves around using ``reaquire.ensure()`` to load dependencies asynchronously. Webpack 2 will allow ``System.load()`` to accomplish the same thing.
### Codesplitting
```js
const login = require('./login') // will be bundled
login(() => {
  // hints to webpack to split this code out of the bundle
  // but "ensure" that its available before the callback runs
  require.ensure([], () => {
    var willNotBeBundled = require('./DashboardPage')
  })
})
```

### Codesplitting + React Router
```js
const AccountRoute = {
  path: '/account/:accountId',
  
  // React Router will call this to get the component
  getComponent(location, cb) {
    // webpack will put it in a different "chunk" file
    // and load it when this gets called
    require.ensure([], () => {
      cb(null, require('./components/Account'))
    })
  },

  // Same thing for the child routes
  getChildRoutes(location, cb) {
    require.ensure([], () => {
      cb(null, [
        require('./routes/Invoices'),
        require('./routes/People'),
      ])
    })
  }
}
```
