## Asynchronous JavaScript

- In previous sections we considered cases that were strictly synchronous, that is every program ran from top to bottom in the order it was given. If the program were run it would generally be completed within milliseconds. In many applications we must consider how the program will behave over a period of time that may range from milliseconds to hours or even years. Instances where an application makes an API call, retrieves information from a database, or perform a repeated task over a time interval (i.e. Animations), all require a response, this takes an indeterminant amount of time and therefore the application must do one of two things. Either wait for the response or continue executing code until the response comes back, then return. The latter is what we consider to be Asynchronous JavaScript. Synchronous code in a single threaded language like JS is almost always a bad idea, just imagine if you made an API request in a program that is synchronous. While the program awaits for a response nothing else can be done because we have effectively blocked the call stack (more on this later).

### JavaScript Runtime

- This section assumes a fair amount of knowledge about the JS runtime, and general computing concepts. To provide some context, I will take a moment to dive into some of these areas.

- [Javascript is a single threaded single concurrent language, meaning it can handle one task at a time or a piece of code at a time. It has a single call stack which along with other parts like heap, queue constitutes the Javascript Concurrency Model (implemented inside of V8). ](https://medium.com/@gaurav.pandvia/understanding-javascript-function-executions-tasks-event-loop-call-stack-more-part-1-5683dea1f5ec)

![fig1](/Part-3-Callbacks-Promises-AsyncAwait/images/fig1.png)

#### Stack

- The stack or call stack records function calls to keep track of where and what is currently being executed. If you have ever used a debugger tool you may have noticed that the tool itself provides the output of the call stack, and each time you step through your program the call stack changes. This is because the call stack constantly has functions pushed or popped off it whether they are being called or returning a value.

```js
function foo(b) {
  var a = 10;
  return a + b + 11;
}

function bar(x) {
  var y = 3;
  return foo(x * y);
}

console.log(bar(7)); //returns 42
```

- In the example above we can see that when the function `bar()` is called that it returns a closure containing the function `foo()` which returns a value. If we were to break this down from the call stacks perspective it would looks something like this.

| Frame-1 | Frame-2            | Frame-3             | Frame-4             | Frame-5                        | Frame-6                       | Frame-7                                    | Frame-8 |
| ------- | ------------------ | ------------------- | ------------------- | ------------------------------ | ----------------------------- | ------------------------------------------ | ------- |
|         |                    |                     | foo(21)             | foo(21) - returns 42, pops off |                               |                                            |         |
|         |                    | bar(7)              | bar(7)              | bar(7)                         | bar(7) - returns 42, pops off |                                            |         |
|         | console.log(bar()) | console.log(bar(7)) | console.log(bar(7)) | console.log(bar(7))            | console.log(bar(7))           | console.log(bar(7)) - returns 42, pops off |         |
| main()  | main()             | main()              | main()              | main()                         | main()                        | main()                                     | main()  |

#### Heap

- The heap is where memory is allocated, therefore variables, and objects are added and removed here.

#### Queue

- The message queue is a list of messages that are waiting to be processed, each message has an associated callback function. When the stack is empty the message queue is checked, if there is a callback function available it will be moved to the stack. This happens when asynchronous events occur in the program i.e. button click, http request, etc, provided that a callback or promise is associated with the event.

#### Event Loop

- When JS is executed it is done so inside of a hosting environment, traditionally this has been web browsers however with the advent of Node.js JS is available in servers, IoT devices, cars, etc. Host enviroments can have APIs that are used to manage requests such as DOM events, HTTP requests, and timers. If a function interacts with the API it is asynchronous as the execution is external to the JS program. At an abstract level when a function of this natures is called in the stack it sends a message and callback function to the API, then pops off the stack. When the API resolves the request, the callback function is moved to the message queue. **The event loop manages the message queue by inserting the callback functions onto the call stack when it is empty**. The following psuedo code demonstrates this process in a very high level manner.

```js
function asyncFunc() {
  console.log('Prints First');
  ajax('https://www.somerandomurl.com', function() {
    console.log('Print Third');
  });
}

asyncFunc();

console.log('Prints Second');

// 'Prints First'
// 'Prints Second'
// 'Prints Third'
```

1. `asyncFunc()` is added to call stack
2. `console.log('Prints First')` is added to call stack, then removed from call stack
3. `ajax()` is added to call stack, makes request to API, then removed from call stack
4. `asyncFunc()` then removed from call stack
5. `console.log('Prints Second')` is added to call stack
6. API resolves request, moves callback to message queue
7. `console.log('Prints Second')` then removed from call stack
8. Call stack empty, event loop moves callback to stack from message queue
9. `console.log('Print Third')` is added to call stack, then removed from call stack
10. Finished
