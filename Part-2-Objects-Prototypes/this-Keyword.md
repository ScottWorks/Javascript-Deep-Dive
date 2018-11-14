## `this` Keyword

- Some common misconceptions about the `this` keyword include:

  1. Refers to the function itself
  2. Refers to the functions lexical scope

- The `this` keyword is actually not a binding created when written in code but rather at runtime, the nature of the binding is subject to changes dur to the conditions of the functions invocaton. The `this` binding has nothing to do with the function declaration but rather which manner it has been declared in.

- According to [You Dont Know JS](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch1.md) `this` is actually a binding that is made when a function is invoked, and what it references is determined entirely by the call-site where the function is called, how the function was invoked, what parameters were passed, etc. One of the properties of this record is the `this` reference which will be used for the duration of that function's execution.

- It is important to understand that the 'call-site' refers to the place in code logic where the function is called and not where it was declared. It is helpful to think about the call-stack or the stack of functions that have been called to get us to the current moment in execution.

```js
function baz() {
  // call-stack is: `baz`
  // so, our call-site is in the global scope

  console.log('baz');
  bar(); // <-- call-site for `bar`
}

function bar() {
  // call-stack is: `baz` -> `bar`
  // so, our call-site is in `baz`

  console.log('bar');
  foo(); // <-- call-site for `foo`
}

function foo() {
  // call-stack is: `baz` -> `bar` -> `foo`
  // so, our call-site is in `bar`

  console.log('foo');
}

baz(); // <-- call-site for `baz`
```