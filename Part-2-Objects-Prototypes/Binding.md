## Binding

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
