## Scope

- Scope is defined as a set of rules that determine how the Javscript Engine can look up variables by its identifier name either in the current scope or any of the nested scopes within.
- Scope is a compile time process, it is during the compilation where the scope decisions are being made.
  - Many people assume JavaScript is an interpreted language when in fact it is actually compiled. This is usually due to the fact that most compiled languages produce an executable once the compilation process is completed. In the case of JavaScript compilation is innocuous, typically source code is delivered to a client however clients contain browser dependent Javascript Engines (Chrome - V8, Mozilla - SpiderMonkey) which actually compile the code before executing.
  - This is evident when a syntax error has been created for example, the JavaScript engine compiles the code and detects the error while parsing the code rather then interpreting the code and stopping at the lines where the error was created.
- Scope tells us what variables belong to which declaration. It is similar to taking different marbles and placing them in seperated buckets.

* Take the following program for example:

```js
var foo = 'bar';

function bar() {
  var foo = 'baz';
}

function baz(foo) {
  foo = 'bam';
  bam = 'yay';
}
```

- If we were to place a `console.log(foo)` between `function bar(){` and `var foo = 'baz';` we would get an `undefined` in return. It is important to keep in mind that the definition in the nearest scope will be used, therefore `foo` will be undefined because within the same scope `foo` has not been declared yet. If we remove `var foo = 'baz';` the `console.log()` will return `'bar'` since the global scope is the nearest scope containing that variable. In summary the JavaScript engine compiles the code in advance and assigns the variable `foo` to the string `'baz'`, when the code is executed following compilation the variable `foo` returns an `undefined` rather than `'bar'` since `foo` is contained within the `bar()` declaration's scope.
  - It is also worth noting that having two or more variables with the same name declaration is called shadowing.
- To access the globally scoped variable `foo` we can use the `window` object, `window.foo`
- Our third globally scoped declaration `function baz(foo) {` contains a parameter, this parameter creates a decalartion that is local to the scope of `baz()`
- Looking at `bam = 'yay';` we notice that there is no formal declaration provided. Although it is tempting make assumptions we must keep in mind the following:
  - The following line within the scope of `baz()` can contain a variable declaration for `bam`
  - The following line below the scope of `baz()` can contain a global variable declaration for `bam`
  - Another file can contain a global variable declaration for `bam`
  - Therefore from the compilers perspective we cannot make any additional assumptions other than it is not a formal declaration.
  - If `bam` is not defined, then the variable `bam` is implicitly defined as a global variable at runtime.

### Function Expressions

- A function expression `var thisFunction = function thatFunction(){}` is different than a function declaration `function thisFunction(){}` because you are creating an expression by assingning a function to a variable.
  - Function expressions have two types, named `var namedFunction = function namedFunction(){}` and anonymous `var thisFunction = function(){}`.
  - In the case of the named function only the expression on the LHS is available to the global scope.
  - Although the anonymous function expression is the most popular type the named function does have several benefits:
    1. Function self-referencing
    2. Debuggabled stack traces
    3. Self-documenting code

### Lexical Scope

- The first phase of compliation known as 'lexing' or 'tokenizing'. This is a process where a string of source code characters are examined then assigned semantic meaning to the tokens after parsing.
- Lexical scope refers to scope which is defined during the lexing phase of compilation. The scope itself is defined when the code is written by a programmer, therefore it is based on the variables and blocks of scope that are user-defined.

- It is easy to think of scope as a series of nested bubbles as shown below:

```js
function foo(a) {
  var b = a * 2;

  function bar(c) {
    console.log(a, b, c);
  }

  bar(b * 3);
}

foo(2); // 2 4 12
```

- Bubble 1 encompasses the global scope, and has just one identifier in it: foo.

- Bubble 2 encompasses the scope of foo, which includes the three identifiers: a, bar and b.

- Bubble 3 encompasses the scope of bar, and it includes just one identifier: c.

![Lexical Scope](/Part-1-Lexical-Scope-and-Closures/images/lex-scope-bubble.png)

### Function Scope

- It is possible to encapsulate variables that may shadow one another (resulting in collisions) by using function scope. Simply declare a funciton and place your variable of interest inside.
- In the case that the variable encapsulation is ephermeral, it is possible to wrap the function statement (creating a function expression) then invoke it after as follows:

```js
// Without encapsulation
// foo is defined two times in the same scope
var foo1 = 'bar';
var foo1 = 'bar2';

console.log(foo1); // bar2

// With encapsulation

var foo2 = 'bar';

(function baz() {
  var foo2 = 'bar2';
  console.log(foo2); // bar2
})();

console.log(foo2); // bar
```

### Block Scoping

- Block scoping refers to the scope of statements i.e. For-Loop, While, If-Else, Object literals { }, etc.
- Using the ES6 `let` or `cosnt` declaration we can limit the scope of variables to the block.

- Using `var` (function scoped)

```js
function diff(x, y) {
  if (x > y) {
    var tmp = x;
    x = y;
    y = tmp;
  }
  console.log(tmp); // prints value of x
  return y - x;
}
```

- Using `let` (block scoped)

```js
function diff(x, y) {
  if (x > y) {
    let tmp = x;
    x = y;
    y = tmp;
  }
  console.log(tmp); // returns reference error
  return y - x;
}
```

- Sidenote: The `const` keyword creates a varible that cannot be reassigned, not a value that is immutable.

```js
var a = 2;
a++; // 3

const b = 2;
b++; // Error

const c = [2];
c[0]++; // 3 <-- This could be a problem
```

## Hoisting

- Hoisting is the process by which the JavaScript compiler moves declarations (variable and function) to the top of the code logic **for each scope**.
  - Note that this only applies to declarations, statements are not hoisted.
  - Functions are prioritized first therefore they come before variables that are hoisted.
- In JavaScript the following will have two completely different types of behaviour:

```js
a = 2;

var a;

console.log(a); // prints 2

// ---------------------------------

console.log(b); // prints undefined

var b = 2;
```

- The reason for this is because the variables are being hoisted upon compilation. This would look like the following if we wanted to visualize it.

```js
var a;
var b;

a = 2;

console.log(a); // prints 2

// ---------------------------------

console.log(b); // prints undefined

b = 2;
```

- It is important to remember that an assignment such as `var a = 2;` is actually done in two phases; during the compilation phase the declaration is processed, then during execution the assignment of a value to the target is handled.
- The `let` and `const` does not initialize their variable after being hoisted and will therefore result in a 'ReferenceError'. The reason for this is mainly because it is a poor coding style to use a variable before assigning it.
