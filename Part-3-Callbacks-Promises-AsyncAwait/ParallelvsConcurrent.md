## Parallel vs Concurrent Programming

### Parallel Threading

#### Multi-Thread

![fig2](/Part-3-Callbacks-Promises-AsyncAwait/images/fig2.png)

- Parallel computing leverages two different mechanisms; threads, and processes. They can execute independently or simultaneously across multiple processors or computers. By contrast a single threaded system executes tasks serially and prevents any parallel access or changes to shared state. These two systems can coexist in the form of a cooperating event loop in seperate threads.

- Multi-threading certainly has advantages however, if used incorrectly a program can exhibit nondeterministic behavior. Consider the following lines of code:

```js
var a = 20;

function foo() {
  a = a + 1;
}

function bar() {
  a = a * 2;
}

// ajax(..) is some arbitrary Ajax function given by a library
ajax('http://some.url.1', foo);
ajax('http://some.url.2', bar);
```

- Notice the asynchornous `ajax()` calls at the end of the program which actually creates a "race condition", with multi-threading it is possible that this program can return 21, or 44 depending on which `ajax` call returns first and also depending on how the different threads manage the shared temporary state. To further understand the latter case we must keep in mind that multiple threads are now managing code execution, therefore each step in the process must be broken down into low-level operations as shown below:

```
Thread 1

foo():
  a. load value of `a` in `X`
  b. store `1` in `Y`
  c. add `X` and `Y`, store result in `X`
  d. store value of `X` in `a`
```

```
Thread 2

bar():
  a. load value of `a` in `X`
  b. store `2` in `Y`
  c. multiply `X` and `Y`, store result in `X`
  d. store value of `X` in `a`
```

```
Scenario 1

1a  (load value of `a` in `X`   ==> `20`)
2a  (load value of `a` in `X`   ==> `20`)
1b  (store `1` in `Y`   ==> `1`)
2b  (store `2` in `Y`   ==> `2`)
1c  (add `X` and `Y`, store result in `X`   ==> `22`)
1d  (store value of `X` in `a`   ==> `22`)
2c  (multiply `X` and `Y`, store result in `X`   ==> `44`)
2d  (store value of `X` in `a`   ==> `44`)
```

```
Scenario 2

1a  (load value of `a` in `X`   ==> `20`)
2a  (load value of `a` in `X`   ==> `20`)
2b  (store `2` in `Y`   ==> `2`)
1b  (store `1` in `Y`   ==> `1`)
2c  (multiply `X` and `Y`, store result in `X`   ==> `20`)
1c  (add `X` and `Y`, store result in `X`   ==> `21`)
1d  (store value of `X` in `a`   ==> `21`)
2d  (store value of `X` in `a`   ==> `21`)
```

#### Single-Thread

![fig3](/Part-3-Callbacks-Promises-AsyncAwait/images/fig3.png)

- By contrast a single-thread is just what it sounds like, a single thread that treats functions "atomically", this means that things are executed in sequence, one chunck of code only runs after another has completed. Take the following code snippet for example:

```js
var a = 1;
var b = 2;

function foo() {
  a++;
  b = b * a;
  a = b + 3;
}

function bar() {
  b--;
  a = 8 + b;
  b = a * 2;
}

// ajax(..) is some arbitrary Ajax function given by a library
ajax('http://some.url.1', foo); // returns 11 or 183
ajax('http://some.url.2', bar); // returns 22 or 180
```

- Notice we have the same asynchronous nature and "race condition" at play here however by comparison this example is more deterministic than the previous one.

### Concurrency

- When something is described to be 'concurrent' it means that two or more seperate instances occur at the same time. We will use the term 'concurrency' to describe instances where two or more processes are executing simealtanously regardless of their location (same processor/ core). This is different from parallel threading in the sense that concurrency occurs at the "process"-level or task-level rather than the operation-level (seperate-processor threads). To help convey this idea it helps to visualize two seperate processes; proces A (in blue) and process B (in orange), we can see that the concurrent program interleaves each process task where as the parallel program executes them on two seperate threads.

![fig](/Part-3-Callbacks-Promises-AsyncAwait/images/fig4.jpg)

- Simialr to parallel threading, concurrent programs can easily result in 'race conditions' if extra care is not taken. Take the following code for example:

```js
var res = [];

function response(data) {
  res.push(data);
}

// ajax(..) is some arbitrary Ajax function given by a library
ajax('http://some.url.1', response);
ajax('http://some.url.2', response);
```

- What happens here is that the `res` array contains the responses from the `ajax` call however these responses may be in different order depending on which response came back first. We can remedy this by specifying the order in which the array stores the responses:

```js
var res = [];

function response(data) {
  if (data.url == 'http://some.url.1') {
    res[0] = data;
  } else if (data.url == 'http://some.url.2') {
    res[1] = data;
  }
}

// ajax(..) is some arbitrary Ajax function given by a library
ajax('http://some.url.1', response);
ajax('http://some.url.2', response);
```

- We may also choose to use conditional logic to prevent `undefined` from being passed in such that we "gate" the code logic that would otherwise be run. Additionally we can perform "cooperative concurrency" where we break up a long-running process into steps or batches that allow other concurrent "processes" to interleave their operations. Take the following code for example:

```js
var res = [];

// `response(..)` receives array of results from the Ajax call
function response(data) {
  // add onto existing `res` array
  res = res.concat(
    // make a new transformed array with all `data` values doubled
    data.map(function(val) {
      return val * 2;
    })
  );
}

// ajax(..) is some arbitrary Ajax function given by a library
ajax('http://some.url.1', response);
ajax('http://some.url.2', response);
```

- We can see that the `ajax` request returns a response array that is concatnated onto the `res` array and each element in the array is doubled. If we did this to an array that contained 10,000 entries then it would take an insignificant amount of time however if the array contained 10 million entries then the significance would be far greater as we would not be able to perform other actions since the event loop is effectively blocked. Obviously this is not an ideal scenario however we can manage this by processing the results in asynchronous batches. This would free up the event loop much more often although the overall time of processing may be extended.

```js
var res = [];

// `response(..)` receives array of results from the Ajax call
function response(data) {
  // let's just do 1000 at a time
  var chunk = data.splice(0, 1000);

  // add onto existing `res` array
  res = res.concat(
    // make a new transformed array with all `chunk` values doubled
    chunk.map(function(val) {
      return val * 2;
    })
  );

  // anything left to process?
  if (data.length > 0) {
    // async schedule next batch
    setTimeout(function() {
      response(data);
    }, 0);
  }
}

// ajax(..) is some arbitrary Ajax function given by a library
ajax('http://some.url.1', response);
ajax('http://some.url.2', response);
```

- As you can see we are processing the array in 1,000 entry increments, then we use the `setTimeout()` method to tell the host enviroment to pull the `response` function off the call stack for 0 milliseconds, move it to the message queue then put it back on the call stack when it empties. This frees up the call stack to execute other code while the array is processed. **It is important to mention that this method does not preserve order, if that is an important factor it would be necessary to incorporate other techniques such as gating**.
