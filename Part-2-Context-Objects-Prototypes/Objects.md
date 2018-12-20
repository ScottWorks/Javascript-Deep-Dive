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

#### Object Access

- Object values can be accessed using either 'dot' or 'square-bracket' notation.

```js
var car = {
  make: 'BMW'
  model: 'M4'
  year: 2019
}

// Dot Notation
car.make // 'BMW'
car.model // 'M4'
car.year // 2019

// Square-Bracket Notation
car['make'] // 'BMW'
car['model'] // 'M4'
car['year'] // 2019
```

- We can also use the 'square-bracket' notation to dynamically add new keys/ values to an existing object for variables that may have non alphabetic characters.

```js
var car = {
  make: 'BMW'
  model: 'M4'
  year: 2019
}

car['driver-passenger'] = 'Me-You'

// car = {
//   make: 'BMW'
//   model: 'M4'
//   year: 2019,
//   driver-passenger: 'Me-You',
// }
```

### Arrays

- Arrays are a commonly used object that structures its stored values using numeric indicies (non-negative integers). The cool part about Arrays is that since they are in fact objects they can store information that is seperate from the values stored in the array.

```js
var myArray = ['foo', 42, 'bar'];

myArray.baz = 'baz';

myArray.length; // 3

myArray.baz; // "baz"
```

### Object Descriptors

- As of ES5 all properties are described in terms of a property descriptor.

```js
var myObject = {
  a: 2
};

Object.getOwnPropertyDescriptor(myObject, 'a');
// {
//    value: 2,
//    writable: true,
//    enumerable: true,
//    configurable: true
// }
```

- It is also possible to add a new property or modify an existing one (assuming the property is configurable)

```js
var myObject = {};

Object.defineProperty(myObject, 'a', {
  value: 2,
  writable: true,
  configurable: true,
  enumerable: true
});

myObject.a; // 2
```

- I wont go into detail about each of these property descriptors, you can find more information [here](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch3.md#property-descriptors).

### `[[GET]]`

- Property access is performed via a `[[GET]]` operation for the object of interest. The built-in `[[GET]]` operation first inspects the object for a property of the requested name, if the property is found it will return it. If the `[[GET]]` operation cannot find the property then it will traverse the `Prototype` chain until it finds it, otherwise it returns with an `undefined`.

```js
var myObject = {
  a: 2
};

myObject.a; // 2
myObject.b; // undefined
```

### `[[PUT]]`

- The `[[PUT]]` operation behaves a bit differently than the `[[GET]]` operation. If the property at question already exists the `[[PUT]]` algorithm behaves accordingly:

  1. Checks if property is an accessor descriptor, if so, setter is called (if available).
  2. Checks if the data descriptor of the proprty has `writeable` set to `false`. If it is the program will fail silently in non-strict mode or throw a `TypeError` in strict-mode.
  3. As a catch-all the value is set.

- If the property is not present on the object then the `[[PUT]]` opertation becomes even more compicated (more on this later in [ProtoTypes](/Part-2-Context-Objects-Prototypes/Prototypes.md))

## Creating new Objects

- At this point we are familiar with some of the built-in objects that are offered out of the box in JavaScript. As a programmer you will likely be interested in creating your own objects at some point, this is basically mandatory if you choose to do any OOP. There are two methods in which we can achieve this; using the `Object Initializer`, or a `Constructor Function`.

### Object Initializer

- The `Object Initializer` method of object creation is fairly straightfoward. In fact it was implicitly demondstated a few times earlier. Essentially we create an object using literal notation, in otherwords we explicitly state what the properties (identifiers) of the object are.

```js
var objLiteral = {
  see: 'its',
  that: 'easy'
};
```

### Constructor Functions

- As we mentioned before our alternative to the `Object Initializer` is the `Constructor Function`. The constructor function allows us to instantiate an object by invoking that function in conjunction with the `new` operator. By doing so we can create seperate instances of the Object. Later we will discuss the importance of the `this` keyword, and how inheritance works in JavaScript.

```js
// Construtctor Function
function Car(make, model, year) {
  this.make = make;
  this.model = model;
  this.year = year;

  this.displayCar = function() {
    return `${this.make} - ${this.model} - ${this.year}`;
  };
}

var roadrace = new Car('Porsche', 'GT3RSR', '2019');
var rally = new Car('Subaru', 'WRX STi', '2019');
var commuter = new Car('Audi', 'RS6 Avant', '2019');

console.log(roadrace.displayCar()); // Porsche - GT3RSR - 2019
console.log(rally.displayCar()); // Subaru - WRX STi - 2019
console.log(commuter.displayCar()); // Audi - RS6 Avant - 2019
```
