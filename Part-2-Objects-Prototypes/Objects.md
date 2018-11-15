## Objects

### Object Syntax

- In JavaScript objects come in two flavors, literals, and constructed.

```js
// Object Literal

var myObj = {
  key: value
  // ...
};
```

```js
// Constructed Object

var myObj = new Object();
myObj.key = value;
```

### Object Types

- In JS there are 6 primitive types

  - string
  - number
  - boolean
  - null
  - undefined
  - object

- Types 1 - 5 are not actually objects, therefore the age old adage of "... everything in JS is an object" simply is not true.

### Built-In Objects

- In addition to our primitives, there are also special object sub-types known as built-in objects. The fact that some of these objects also have primitive counterparts (`string` - `String`, `number` - `Number`) may lead to notion that these there is a direct relationship when in fact the relationship is fairly complicated.

  - String
  - Number
  - Boolean
  - Object
  - Function
  - Array
  - Date
  - RegExp
  - Error

```js
var strPrimitive = 'I am a string';
typeof strPrimitive; // "string"
strPrimitive instanceof String; // false

var strObject = new String('I am a string');
typeof strObject; // "object"
strObject instanceof String; // true

// inspect the object sub-type
Object.prototype.toString.call(strObject); // [object String]

console.log(strPrimitive.length); // 13
console.log(strPrimitive.charAt(3)); // "m"
```

- We can observe from the above code snippet that the `strObject` was created by the `String` constructor and is therefore an object. By contrast `strPrimitive` is not an object. This is odd many of us have used the `.length` method to find the length of a primitive literal and it works without fail. The reason for this is that the JavaScript engine coerces the primitive into object form. I should also note that this is the preferred way, in general the constructed form should only be used if you require the extra options.

#### Object Properties

- A quick note on semantics; when discussing property access on an object it is common to trip up and imply that a function belongs to an object, this is not how objects work in JS. Functions are not owned by objects, although some functions leverage `this` keywords which at some point in time refer to an object. While this may be the case the function is dynamically bound at run-time, and dependent on the call-site therefore the relationship to the object is indirect.

```js
function foo() {
  console.log('foo');
}

var someFoo = foo; // variable reference to `foo`

var myObject = {
  someFoo: foo
};

foo; // function foo(){..}

someFoo; // function foo(){..}

myObject.someFoo; // function foo(){..}
```

### Arrays

- Arrays are a commonly used object that structures its stored values using numeric indicies (non-negative integers). The cool part about Arrays is that since they are in fact objects they can store information that is seperate from the values stored in the array.

```js
var myArray = ['foo', 42, 'bar'];

myArray.baz = 'baz';

myArray.length; // 3

myArray.baz; // "baz"
```

### [Further Reading](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch3.md#property-descriptors)