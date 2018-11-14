## Object Prototypes

Note: this section follows closely with [You Dont Know JS: this & Object Prototypes](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch1.md)

### `this` Keyword

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

### Binding

- Depending on the call-site there are 4 different types of binding (patterns) that can apply.

#### Default Binding

- As the name implies Default Binding is the type of binding that applies when nothing special has been done to our function call. When this is the case the function that contains this will actually refer to the global object. Some context may be helpful here, as we explore in lexical scope, there is a global scope; there is also a global object which is the same thing as global scope but relies on an entirely different system.

```js
function foo() {
  console.log(this.a); // refers to the global object by default
}

var a = 2;

foo(); // 2
```

#### Implicit Binding

- The word implicit itself is defined as "implied though not plainly expressed", this is helpful because implicit binding can be thought of as binding which is implied (opposed to explicitly bound, more on that later). In this case a function can be declared then later on added as a reference property to an object, this doesn't necessarily mean that the object has strict ownership over the function but rather it has access to the logic.

```js
function foo() {
  console.log(this.a);
}

var obj = {
  a: 2,
  foo: foo
};

obj.foo(); // 2
```

```js
function accelerate() {
  this.speed += 20;
  return this.speed;
  // 3) the `this` keyword will search for its context until it finds it, in this case it is critRacer
}

let critRacer = {
  speed: 20,
  goFaster: accelerate
  // 2) points to accelerate function above to function accelerate()
};

critRacer.goFaster();
// 1) when we invoke goFaster() the question is what is the value of the `this` keyword
```

- Although this seems straightforward we must remember that the call-sie is what determines the type of binding which is used. If we fail to pay close attention to this we may create a function call that actually ends up losing the implicit binding, which would result in the default binding being used.

```js
function foo() {
  console.log(this.a);
}

var obj = {
  a: 2,
  foo: foo
};

var bar = obj.foo; // function reference/alias!

var a = 'oops, global'; // `a` also property on global object

bar(); // "oops, global"
```

- Another example of losing the implicit binding with a callback function...

```js
function foo() {
  console.log(this.a);
}

function doFoo(fn) {
  // `fn` is just another reference to `foo`

  fn(); // <-- call-site!
}

var obj = {
  a: 2,
  foo: foo
};

var a = 'oops, global'; // `a` also property on global object

doFoo(obj.foo); // "oops, global"
```

#### Explicit Binding

#### `new` Binding
