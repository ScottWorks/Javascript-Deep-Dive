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
  baz();
}

foo(); // bar
```

### Loops and Closures

- Given the following code snippet, we would expect the following output: `1 2 3 4 5`, instead we get `6 6 6 6 6`. Why is this?

```js
for (var i = 1; i <= 5; i++) {
  setTimeout(function timer() {
    console.log(i);
  }, i * 1000);
}
```

- Each cycle through the loop increments `i` by 1, and the `timer()` function is executed 5 times independent of the last iteration. The problem here is that the execution occurs within the same parent scope, the function itself has a delay of 1 second on the first iteration, by the time it returns the loop has already reach its stopping point of `i === 6`. At this point `setTimeout()` will continue to fire off another 4 times returning 6 each times.

```
Iteration: 1
Globally Scoped i: 1
timer(): Waiting for 1 second

Iteration: 2
Globally Scoped i: 2
timer(): Waiting for 1 second

Iteration: 3
Globally Scoped i: 3
timer(): Waiting for 1 second

.
.
.

Iteration: 6
Globally Scoped i: 6
timer(): Waiting for 1 second

Iteration: 6
Globally Scoped i: 6
timer(): Ok im ready, print 6

.
.
.

Iteration: 6
Globally Scoped i: 6
timer(): Ok im ready, print 6
```

- To fix this we fundamentally need to do one thing, which is isolate the scope of `i` between iterations by either creating a function scope or a block scope. The function scope can be achieved by leveraging the usage of an Immediately Invoke Function Expression...

```js
for (var i = 1; i <= 5; i++) {
  (function(j) {
    setTimeout(function timer() {
      console.log(j);
    }, j * 1000);
  })(i);
}
```

- Notice that we essentially store the current `i` value after each iteraction by adding it to the function scope of the IIFE

- Alternatively we can provide the block scope with its own instance of `i`...

```js
for (let i = 1; i <= 5; i++) {
  setTimeout(function timer() {
    console.log(i);
  }, i * 1000);
}
```

Either of these approaches would result in the following:

```
Iteration: 1
Locally Scoped i: 1
timer(): Waiting for 1 second

Iteration: 2
Locally Scoped i: 2
timer(): Waiting for 1 second

Iteration: 3
Locally Scoped i: 3
timer(): Waiting for 1 second

.
.
.

Iteration: 6
Locally Scoped i: 6
timer(): Waiting for 1 second

Iteration: 6
Locally Scoped i: 1
timer(): Ok im ready, print 1

Iteration: 6
Locally Scoped i: 2
timer(): Ok im ready, print 2

.
.
.

Iteration: 6
Locally Scoped i: 5
timer(): Ok im ready, print 5
```

### Final Thoughts

- Closures are good for several things:

1.  Keeping data private and encapsulated (i.e. module pattern)

2.  Allowing us to create multiple instances that are independent.
    - Keep in mind that the inner function isnt actually returning the variable to the outer function, therefore we must interact with the variables in the inner function.

```js
function outer() {
  var count = 0;
  return function inner() {
    count++;
    console.log(count);
  };
}

var closureTest1 = outer();
closureTest1(); // returns 1
closureTest1(); // returns 2

var closureTest2 = outer();
closureTest2(); // returns 1, independent of closureTest1
```

3.  Asyncronous calls

```js
function delayMessage(msg, delay) {
  setTimeout(() => console.log(msg), delay);
}

delayMessage('Yo I waited', 3000);
console.log('Way before anything is fired off');

for (var i = 0; i < 10; i++) {
  delayMessage(i, i * 500);
}
```
