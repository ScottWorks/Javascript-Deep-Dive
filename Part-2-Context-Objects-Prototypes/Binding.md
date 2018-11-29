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

- Explicit binding is the case where we use a built-in function (i.e. `.call()`, or `.apply()`) to state which object the function references when the `this` keyword is invoked.

```js
function foo() {
  console.log(this.a);
}

var obj = {
  a: 2
};

foo.call(obj); // 2
```

- This method is perhaps more obvious from a readability standpoint however it still has the same problems as implicit binding in terms of explicity being lost. To overcome this we can use the `.bind()` function to "hard bind" the object to the function. Regardless of how the function is invoked in the code logic the function will retain its reference to the object.

```js
function foo(num) {
  console.log(this.name);
  return num;
}

var obj1 = {
  name: 'obj1'
};

var obj2 = {
  name: 'obj2'
};

var bar = foo.bind(obj1);
var b = bar(3); // obj1
console.log(b); // 3

var baz = foo.bind(obj2);
var c = baz(5); // obj2
console.log(c); // 5
```

#### `new` Binding

- To understand what the `new` keyword in JS actually is, it is helpful to have some context. In traditional class-oriented languages classes are instantiated through the following process.

  1. `new` keyword is used to instantiate a class object `var array1 = new Array(...);`
  2. constructor (special method attached to a class) of the class is called
  3. class object is created

- Javascript leverages an identical code pattern however the functionality is entirely different. In JS a constructor is just a function which is not attached to a specific class, nor do they instantiate the class. If we were to elaborate the process followed when the `new` keyword is invoked it would look like this:
  1. a new object is created (or constructed)
  2. the newly constructed object is prototype-linked.
  3. the newly constructed object is set as the `this` binding for that function call
  4. unless the function returns its own alternate object, the `new`-invoked function call will automatically return the newly constructed object.

```js
// when we use the new keyword javascrip will automatically do the following three things:
function Car(make, model, color) {
  // this = {} // automatically makes `this` an object
  // this.prototype = Component // makes any method defined a prototype that is binded to the object and attached to the class

  this.make = make;
  this.model = model;
  this.color = color;
  this.mileage = 0;
  // return this // automatically returns this
}

// Downsides to using prototpyes is that it could conflict with prototype inheritance
// The benefit is that all the prototypes have the exact same function, however the class function does the same thing
Car.prototype.driveCar = function() {
  this.mileage += 10;
};

var myNewCar = new Car('Porsche', 'GT3', 'Gray');
myNewCar.drivecar();

class Truck {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
    this.mileage = 0;
  }

  drive() {
    this.mileage += 19;
  }
}
```

- "By calling `foo(..)` with `new` in front of it, we've constructed a new object and set that new object as the `this` for the call of `foo(..)`. **So `new` is the final way that a function call's `this` can be bound.** We'll call this new binding."

### [Determining `this`](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch2.md#determining-this)

- Now, we can summarize the rules for determining this from a function call's call-site, in their order of precedence. Ask these questions in this order, and stop when the first rule applies.

- Is the function called with new (**new binding**)? If so, this is the newly constructed object.

  - `var bar = new foo()`

- Is the function called with call or apply (**explicit binding**), even hidden inside a bind hard binding? If so, this is the explicitly specified object.

  - `var bar = foo.call( obj2 )`

- Is the function called with a context (**implicit binding**), otherwise known as an owning or containing object? If so, this is that context object.

  - `var bar = obj1.foo()`

- Otherwise, default the this (**default binding**). If in strict mode, pick undefined, otherwise pick the global object.
  - `var bar = foo()`

### ES6 Arrow-Function Binding

- Arrow functions introduced in ES6 do not follow the 4 rules of binding, rather they will adopt the `this` of the enclosing (function or global) scope. It is important to note that this binding cannot be overridden.

```js
function foo() {
  // return an arrow function
  return (a) => {
    // `this` here is lexically adopted from `foo()`
    console.log(this.a);
  };
}

var obj1 = {
  a: 2
};

var obj2 = {
  a: 3
};

var bar = foo.call(obj1);
bar.call(obj2); // 2, not 3!
```

- Using `this` and arrow functions can be counter productive and lead to problems down the road, therefore it is advisable (by Kyle Simpson) to either use only lexical scope and forget the pretense of `this` style code or to emprace `this` style mechanisms completely including `.bind(...)`.

- [A program can effectively use both styles of code (lexical and this), but inside of the same function, and indeed for the same sorts of look-ups, mixing the two mechanisms is usually asking for harder-to-maintain code, and probably working too hard to be clever.](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch2.md#lexical-this)
