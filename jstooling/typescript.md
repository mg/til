# Using Typescript with a ReactJS/BableJS/Wepback pipeline
[Source](https://medium.com/@clayallsopp/incrementally-migrating-javascript-to-typescript-565020e49c88#.yqenwlyc3)

As of 1.8, *Typescript* allows us to pass through JS files which allows for a easy migration path of an existing project.

### Step 1: Install Typescript
```
npm install --save-dev Typescript
```

### Step 2: Setup Typescript configuration
```json
// tsconfig.json
{
  "compilerOptions": {
    "allowJs": true,
    "outDir": "tsDist",
    "module": "es6",
    "target": "es6"
  },
  "exclude": [
    "node_modules",
    "tsDist",
    "dist",
    "webpack.config.js"
  ]
}
```
``allowJs``is an *1.8* option that allows for js passthrough.

### Step 2: Direct Webpack to Typescript's output
```js
module.exports = {
  entry: './tsDist/index.js',
  // ...
}
```

### Step 3: Change build step
```json
// package.json
"scripts": {
  "build": "rm -rf ./dist/ && rm -rf ./tsDist/ && tsc && webpack && open index.html"
}
```
