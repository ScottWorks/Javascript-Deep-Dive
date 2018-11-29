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
