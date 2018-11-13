## Closure

- Closure is when a function "remembers" its lexical scope even when the function is executed outside that lexical scope. It is a characteristic of functions in a lexically scoped envrioment that allows the function to continue accessing variables outside of itself even when it is executed somewhere else.
  - Visually this looks like like a function that executes inside of another function that has its own independent scope.

```js
function foo() {
  var bar = 'bar';

  function baz() {
    console.log(bar);
  }

  bam(baz);
}

function bam(baz) {
  // This is a closure
  baz(); // bar
}

foo();
```
