# ES6 Generators
*[Source material](http://www.2ality.com/2015/03/es6-generators.html)*, sections 1 to 4

Generators behave both as an *Iterable* and an *Observable* (when fulfilling both roles, they are called *Coroutines*). Calling the generator function constructs the generator but does not start it. Calling **next()** first time starts the generator (but no value can be sent with **next()** at that time and it always returns *undefined*).
``` js
function* generator(i) {
  console.log(`In: ${i}`)
  i += 1
  let ret= yield i                          // first next() runs here and stops
  console.log(`Result from yield: ${ret}`)
  i += 1
  ret= yield i                              // next next() runs here and stops
  console.log(`Result from yield: ${ret}`)
}

const gen= generator(1)
gen.next()                                // In: 1
let ret= gen.next('A')                    // Result from yield: A
console.log(`Result from gen: ${ret}`)    // Result form gen: 2
let ret= gen.next('B')                    // Result from yield: B
console.log(`Result from gen: ${ret}`)    // Result form gen: 3
```

## Consumption
Genertors can be consumed by various methods besides calling **next()**:
``` js
  //
  for(let x of genFunc()) {  // for-of loop
  }

  let arr= [...genFunc()] // spread operator to array

  let [x, y, ...rest]= genFunc()   // destructuring
```
## Observer methods
**return()** terminates generator at current **yield**. **throw()** throws an error at current **yield**.

## Recursion
A generator can recursively call another generator via the **yield* ** statement. The return value of the recursion is the return value of the generator called.

### Example: Recursing over a tree
``` js
class BinaryTree {
  constructor(value, left=null, right=null) {
      this.value = value;
      this.left = left;
      this.right = right;
  }

  /** Prefix iteration */
  * [Symbol.iterator]() {
      yield this.value;
      if (this.left) {
          yield* this.left;
      }
      if (this.right) {
          yield* this.right;
      }
  }
}
```

## Misc
### Iterator interface
``` js
interface Iterable {
    [Symbol.iterator]() : Iterator;
}
interface Iterator {
    next() : IteratorResult;
    return?(value? : any) : IteratorResult;
}
interface IteratorResult {
    value : any;
    done : boolean;
}
```

### Observer interface
``` js
interface Observer {
  next(value? : any) : void;
  return(value? : any) : void;
  throw(error) : void;
}
```
