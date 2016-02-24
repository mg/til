# Pattern: API Middleware
[Source](https://auth0.com/blog/2016/01/04/secure-your-react-and-redux-app-with-jwt-authentication/)

Use a Redux Middleware to watch for specific actions and execute async API calls and forward results to store.

### Middleware
```js
const BASE_URL = 'API-ENDPOINT'

function callApi(endpoint, authenticated) {
  // get JWT authentication token from locale storage
  let token = localStorage.getItem('id_token') || null
  let config = {}

  if(authenticated) {
    if(token) {
      config = {
        headers: { 'Authorization': `Bearer ${token}` }
      }
    } else {
      throw "No token saved!"
    }
  }

  return fetch(BASE_URL + endpoint, config)
    .then(response =>
      response.text().then(text => ({ text, response }))
    ).then(({ text, response }) => {
      if (!response.ok) {
        return Promise.reject(text)
      }
      return text
    }).catch(err => console.log(err))
}

export const CALL_API = Symbol('Call API')

export default store => next => action => {
  const callAPI = action[CALL_API]

  // So the middleware doesn't get applied to every single action
  if (typeof callAPI === 'undefined') {
    return next(action)
  }

  let { endpoint, types, authenticated } = callAPI
  const [ requestType, successType, errorType ] = types

  // Passing the authenticated boolean back in our data will let us distinguish between normal and secret quotes
  return callApi(endpoint, authenticated).then(
    response =>
      next({
        response,
        authenticated,
        type: successType
      }),
    error => next({
      error: error.message || 'There was an error.',
      type: errorType
    })
  )
}
```

### Actions
```js
import { CALL_API } from './middleware/api'

export const QUOTE_REQUEST = 'QUOTE_REQUEST'
export const QUOTE_SUCCESS = 'QUOTE_SUCCESS'
export const QUOTE_FAILURE = 'QUOTE_FAILURE'

export function fetchQuote() {
  return {
    [CALL_API]: {
      endpoint: 'random-quote',
      authenticated: true,
      types: [QUOTE_REQUEST, QUOTE_SUCCESS, QUOTE_FAILURE]
    }
  }
}
```
