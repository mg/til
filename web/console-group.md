# console.group()
Use ``console.group()`` and ``console.groupEnd()`` to group ``console.log()`` statements together for clarity. Especially useful when working with libraries that are logging a lot of statements.
```js
function someFunctionIWantToUnderstandBetter() {
  try {
    console.group('someFunctionIWantToUnderstandBetter')
    // call functions
  } finally {
    console.groupEnd('someFunctionIWantToUnderstandBetter')
  }
}
```
More *console* functions <https://developer.mozilla.org/en-US/docs/Web/API/Console>
