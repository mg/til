# Ramda
[Source 1](http://fr.umio.us/why-ramda/),
[source 2](http://fr.umio.us/favoring-curry/),
[source 3](https://hughfdjackson.com/javascript/why-curry-helps/),
[Ramda](http://ramdajs.com/),
[Curry](https://github.com/dominictarr/curry)

Make it simple to build complex logic through functional composition. The model is to build the logic before applying it to different data, to build pipelines of functions that we then later use on data to process.

```js
const incomplete= R.filter(R.whereEq({complete: false})

// group by, two methods
const groupByUser= R.groupBy(R.prop('id'))
const groupByUser= R.partition(R.prop('id'))

// compose two functions into one
const activeByUser= R.compose(groupByUser, incomplete)

// sort and reverse sort
const sortByDate= R.map(R.sortBy(R.prop('dueDate')))
const sortByDateDescend= R.compose(R.reverse, sortByDate)

const sortUserTasks= R.compose(sortByDate, activeByUser)

// take N from a list
const topFiveUserTasks = R.compose(R.mapObj(R.take(5)), sortUserTasks)

// Project, aka SQL SELECT
const importantFields = R.project(['title', 'dueDate'])
const importantFieldsFromTopUsers= R.compose(R.mapObj(importantFields), topFiveUserTasks)

```

### Currying
The process to turn a function that expects multiple arguments into one that, when supplied fewer arguments, returns a function that expects the remaining ones. Technically, *currying* is a more specific variant of *partial application* where the resulting function only takes one parameter.

```js
const getIncompleteTaskSummaries= membername =>
  fetchData()
    .then(R.get('tasks'))
    .then(R.filter(R.propEq('username', membername)))
    .then(R.reject(R.propEq('complete', true)))
    .then(R.map(R.pick(['id', 'dueDate', 'title', 'priority'])))
    .then(R.sortBy(R.get('dueDate')))
}
```
