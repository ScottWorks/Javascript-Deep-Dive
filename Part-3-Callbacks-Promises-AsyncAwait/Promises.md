## `Promise`

- `Promise`'s are another way in which we can manage asynchronous behviour in a controlled (or synchronous) manner. By definition it is an object with two internal properties:

  - `state` which is initially "pending" then changes to either fullfilled or rejected
  - `result`, initially undefined, but returns some later value or an error.

![fig5](/Part-3-Callbacks-Promises-AsyncAwait/images/fig5.png)

- The `Promise` acts as a proxy for a value that is not known when the `Promise` is created. Instead of immediately returning the final value, the asynchronous method will return a promise to provide a value (full-filled or rejected) at some point in the future. You may be wondering what makes this different then callbacks? With callbacks there is no guarentee about the future value that is being returned, in fact there are [several things that can go wrong when using callbacks](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch3.md#promise-trust).

  - Call the callback too early
  - Call the callback too late (or never)
  - Call the callback too few or too many times
  - Fail to pass along any necessary environment/parameters
  - Swallow any errors/exceptions that may happen

- It is important for us to be able to have trust that our function calls will return repeatable answers. `Promise`'s manage this in two ways:

  1. `Promise`'s are immutable, other event handlers cannot change the returned value.
  2. As mentioned earlier `Promise`, promise to provide a value in the future. This is independent of when the event handler is registered, therefore we cannot incur race conditions.
  3. Additionally `Promise`'s give us a means of controlling program flow, as well as handling errors with `return` and `throw`.

### `Promise` in Action

- `Promise` are special because of their ability to be chained together in a sequence of steps. Using the `.then(...)` keyword on a `Promise` will create and return a new `Promise` that is chained to the first. Whatever value is returned from a `.then(...)`'s callbacl function is automatically set as the resolve value of the next `Promise` in the chain.

```js
var p = Promise.resolve([1, 2, 3]);

var p2 = p
  .then(function(arr) {
    console.log(arr); // [1, 2, 3]

    return arr.map((elem) => elem * 2); // resolve p2 w/ 2 * arr[i]
  })
  .then(function(arr) {
    console.log(arr); // [ 2, 4, 6 ]
  });
```

- The next natural thing to do here is to handle some asynchronous action. This can be accomplished by simply returning a `Promise` in one of the `.then(...)`'s. The way that JS handles nested `Promise` is to recurse through a single `.then(...)` before moving on to the next one.

```js
var p = Promise.resolve([1, 2, 3]);

var p2 = p
  .then(function(arr) {
    console.log(arr); // [1, 2, 3]

    return new Promise(function(resolve, reject) {
      // async function
      setTimeout(function() {
        // resolve p2 w/ 2 * arr[i]
        resolve(arr.map((elem) => elem * 2));
      }, 1000);
    });
  })
  .then(function(arr) {
    // prints after 1 second delay
    console.log(arr); // [ 2, 4, 6 ]
  });
```

- We can also use the `.catch()` method which only deals with rejected cases.

```js
var p = new Promise(function(resolve, reject) {
  if (1 > 2) {
    return 'Good Maths';
  }

  throw 'Bad Maths';
});

var p2 = p
  .then(function(result) {
    console.log(result);
  })
  .catch(function(error) {
    console.log(error); // expected output: Bad Maths
  });
```
