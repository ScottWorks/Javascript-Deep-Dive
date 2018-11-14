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

- We can observe from the above code snippet that the `strObject` was created by the `String` constructor and is therefore an object. By contrast `strPrimitive` is not an object. This is odd many of us have used the `.length` method to find the length of a primitive literal and it works without fail. The reason for this is that when a built-in method for the corresponding object is applied to a primitive literal the primitive literal is coerced into the object form. I should also note that this is the preferred way.
