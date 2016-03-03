# Babel 6 Runtime

The runtime support in Babel 6 is split up in two parts, the actual runtime implementation (*bable-runtime*) and code transformation (*babel-plugin-transform-runtime*) that analyzes your code and imports from the runtime what you are using.

### Step 1: Install both packages
```
npm i --save babel-runtime
npm i --save-dev babel-plugin-transform-runtime
```

### Step 2: Edit .babelrc to set up the transformation
```
{
  "presets": ["es2015"],
  "plugins": ["transform-runtime"]
}
```
