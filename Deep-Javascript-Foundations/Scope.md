## Scope

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

### Nested Scope

## Hoisting

## Closure

## Modules
