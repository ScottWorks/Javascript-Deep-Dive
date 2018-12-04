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

  1. `Promise`'s are immutable, other handlers (i.e. `resolve`/ `reject`) cannot change the returned value.
  2. As mentioned earlier `Promise`, promise to provide a value in the future. This is independent of when the event handler is registered, therefore we cannot incur race conditions.
  3. Additionally `Promise`'s give us a means of controlling program flow, as well as handling errors with `return` and `throw`.

### `Promise` in Action

- We create promises using the `new Promise` constructor call, this accepts a handler with two functions as parameters;
  - `resolve` indicates that the job finished successfully, updates `state` to fullfilled and `result` to some value
  - `reject` indicates an error occured, updates `state` to rejected and `result` to some error (or `Error` object)

```js
// Making an HTTP request
function get(url) {
  // Return a new promise.
  return new Promise(function(resolve, reject) {
    // Do the usual XHR stuff
    var req = new XMLHttpRequest();
    req.open('GET', url);

    req.onload = function() {
      // This is called even on 404 etc
      // so check the status
      if (req.status == 200) {
        // Resolve the promise with the response text
        resolve(req.response);
      } else {
        // Otherwise reject with the status text
        // which will hopefully be a meaningful error
        reject(Error(req.statusText));
      }
    };

    // Handle network errors
    req.onerror = function() {
      reject(Error('Network Error'));
    };

    // Make the request
    req.send();
  });
}
```

```js
// Function usage
get('story.json').then(
  function(response) {
    console.log('Success!', response);
  },
  function(error) {
    console.error('Failed!', error);
  }
);
```

[Source](https://developers.google.com/web/fundamentals/primers/promises)

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

/////////// OR \\\\\\\\\\\\

var p = new Promise(function(resolve, reject) {
  var result = [];

  // async function call
  setTimeout(function() {
    result.push([1, 2, 3, 4]);
    resolve(result[0]);
  }, 1000);
}).then(function(result) {
  console.log(result); // [1, 2, 3, 4]
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

- It is important to keep in mind a few things though:

  - You can't cancel a Promise, once created it will begin execution. If you don't handle rejections or exceptions, they get swallowed.
  - You can't determine the state of a Promise, ie whether it's pending, fulfilled or rejected. Or even determine where it is in it's processing while in pending state.
  - If you want to use Promises for recurring values or events, there is a better mechanism/pattern for this scenario called streams.

- If we are using multiple `Promise`'s that need to all be resolved before anything is done then we can use [`Promise.all()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all), to return an array containing all of the resolved `Promise`'s.

```js
var p1 = Promise.resolve(3);
var p2 = 1337;
var p3 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('foo');
  }, 100);
});

Promise.all([p1, p2, p3]).then((values) => {
  console.log(values); // [3, 1337, "foo"]
});
```

- We can also use [`Promise.race()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/race) if we want to return the first `Promise` rather than waiting for all of them. For example suppose that we want to fetch a JSON object from a URL but we want to create a timeout in case it takes too long. We can do the following:

```js
// A Promise that returns a JSON object
function fetchJSON(url) {
  return new Promise((resolve, reject) => {
    $.getJSON(url)
      .done((json) => resolve(json))
      .fail((xhr, status, err) => reject(status + err.message));
  });
}

// A Promise that times out after ms milliseconds
function delay(ms) {
  return new Promise((resolve, reject) => {
    setTimeout(resolve, ms);
  });
}

// Which ever Promise fulfills first is the result passed to our handler
Promise.race([
  fetchJSON('http://www.api.com/profile/currentuser'),
  delay(5000).then(() => {
    user: 'guest';
  })
])
  .then(function(json) {
    // this will be 'guest' if fetch takes longer than 5 sec.
    console.log('user:', json.user);
  })
  .catch(function(err) {
    console.log('error:', err);
  });
```
