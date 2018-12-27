## Prototypes

- Objects in JavaScript use an internal property known as `Prototype` as a means of referencing one object to another. Generally objects have a non-null `Prototype` value upon creation.

```js
var myObject = {
  a: 2
};

myObject.a; // 2
```

- As we mentioned in the [Objects](Part-2-Context-Objects-Prototypes/Objects.md) section when we reference a property on an Object the `[[GET]]` operation checks the object itself for said property. If the property is not found we begin to traverse the `Prototype` chain until it is located. This may look like the following example, where we 'copy' an object and reference a property from the copy. Clearly we never actually define the specific property being referenced however it can be located through the `Prototype` chain. It should be mentioned that if the property did not exist on the copied object then an `undefined` would be returned.

```js
var anotherObject = {
  a: 2
};

// create an object linked to `anotherObject`
var myObject = Object.create(anotherObject);

myObject.a; // 2
myObject.b; // undefined
```

![fig8](/Part-2-Context-Objects-Prototypes/images/fig8.png)

### Class vs `Prototype`

- In traditional class-oritented programming languages creating a new instance of a class is analogous to using a blueprint for building identical copies of a home. Each new home that is created is its own unique instance and this process can be repeated as many times as needed.

- JS does not feature classes natively, instead we create `Objects` that are **linked** via the `Prototype` chain to a common parent object. For each object instance the properties and methods from that are added to parent `Prototype` are not actually being transferred to the child, rather the child references the parent properties and methods through the `Prototype` chain.

- Some common areas people get get tripped up on are generally related to the similarities in syntax that JS shares with other class oriented languages. These similarties include:

  1. usage of the `new` keyword
  2. defining classes with capital letters (`Class Car()`)
  3. the constructor appears to be part of a specific object then the built-in `Object.prototype`:

#### `Object.prototype`

- Every `Prototype` chain is built on top of the `Object.prototype` built-in, reason being that all objects in JavaScript descend from the `Object.prototype` object. This provides us with access to utilities such as `.toString()`, `isPrototypeOf()`, and [more](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/prototype). `Object.prototype` also has contains the constructor property which points back to the `Object()` function. In the image below the circle represents the `Object()` function while the square represents the `Prototype` object (`Object.prototype`).

![fig3](/Part-2-Context-Objects-Prototypes/images/fig3.png)

- Given the following object `Car` we can see how the Car object is chained to the top-level Object via the `Prototype`.

```js
function Car(make, model, year) {
  this.make = make;
  this.model = model;
  this.year = year;

  this.displayCar = function() {
    return `${this.make} - ${this.model} - ${this.year}`;
  };
}

var roadrace = new Car('Porsche', 'GT3RSR', '2019');

console.log(Car.prototype);

// {
//   constructor: ƒ Car(make, model, year),
//   prototype:
//     constructor: ƒ Car(make, model, year)
//     __proto__: {
//       constructor: ƒ Object(),
//       hasOwnProperty: ƒ hasOwnProperty(),
//       isPrototypeOf: ƒ isPrototypeOf(),
//       propertyIsEnumerable: ƒ propertyIsEnumerable(),
//       toLocaleString: ƒ toLocaleString(),
//       toString: ƒ toString(),
//       valueOf: ƒ valueOf(),
//    }
// }
```

- If we add a new method to the `Car.prototype` we would actually be creating a new `Prototype` object called `changeColor()` which is also stored in the `Car.prototype`. We will dive into the `Prototype Chain` shortly.

```js
function Car(make, model, year) {
  this.make = make;
  this.model = model;
  this.year = year;

  this.displayCar = function() {
    return `${this.make} - ${this.model} - ${this.year}`;
  };
}

Car.prototype.changeColor = function(color) {
  this.color = color;
};

var roadrace = new Car('Porsche', 'GT3RSR', '2019');

console.log(Car.prototype);

// {
//   changeColor: ƒ (color)
//     prototype: {
//       constructor: ƒ
//     },
//     __proto__: ƒ (),
//   constructor: ƒ Car(make, model, year),
//     prototype: {
//      changeColor: ƒ (color),
//      constructor: ƒ Car(make, model, year),
//      __proto__: Object
//    }
// }
```

#### `Object.prototype.__proto__`

- In our last example you may have also noticed the `__proto__` on `Object.prototype.`. This is an accessor property, a `getter` and `setter` function that exposes the internal `Prototype` of the object. In other words `__proto__` is the object that is used to traverse the `Prototype` chain to resolve methods and properties.

- Keep in mind that direct usage of `__proto__` is discouraged since it originally was not officially included in the ECMAScript language specification.

#### `Object.prototype.constructor`

- When an object is created the `constructor function` will automatically create a `Prototype` object for the new object. This `Prototype` object is stored as part of the `Object.prototype.constructor` object.

### Prototype Linkage

[Source](http://www.javascripttutorial.net/javascript-prototype/)

- Earlier we discussed the relationship between the `contructor` property (`Object.prototype.constructor`), `Object()` function, and `Prototype` object (`Object.prototype`). Now we must consider how the `Prototype` links newly formed objects to their parent as well as the top-level `Object.protoype`, this plays largely into how Objects inherit their properities and methods in JavaSCript.

- Take for example the constructor function `Foo()`, under the hood JS creates a new function (circle) and the `Foo.prototype` object (square). The function has a property called `.prototype` that is linked to the anonymous and the object has a property called `.constructor` that points to the function. The `Foo.prototype` object is also linked to the `Object.prototype` object via prototype linkage, `[[Prototype]]`.

```js
function Foo(name) {
  this.name = name;
}
```

![fig4](/Part-2-Context-Objects-Prototypes/images/fig4.png)

- Now perhaps we have decided that we need a new method called `whoAmI()`, we can do this by adding it to the `Foo.prototype` object directly.

```js
Foo.prototype.whoAmI = function() {
  return 'I am ' + this.name;
};
```

![fig5](/Part-2-Context-Objects-Prototypes/images/fig5.png)

- We have an awesome constructor function and its finally time to use it. We create two instances `a` and `b`. Both of these object instances point to the same `Foo.prototype` using the prototype linkage.

- One thing that is important to keep in mind is that a and b don't actually store the function `whoAmI()`, only the properties and methods in the constructor function. Therefore if we want to call `whoAmI()` we would need to traverse the prototype linkage until `whoAmI()` is found. Same thing with the `Object.prototype` methods such as `.toString()`, `.valueOf()`, etc.

```js
var a = new Foo('a');
var b = new Foo('b');
```

![fig6](/Part-2-Context-Objects-Prototypes/images/fig6.png)

- Perhaps we have also decided that `b` needs a new function called `say()` we can add this functionality to `b` without affecting `a`.

```js
b.say = function() {
  console.log('Hi from ' + this.whoAmI());
};
```

![fig7](/Part-2-Context-Objects-Prototypes/images/fig7.png)

### Shadowing

- As I stated earlier, if the either `a.whoAmI()` or `b.whoAmI()` are called they would have to traverse the prototype chain until `whoAmI()` is found. It is also totally possible to either intentionally or unintentionally create the `whoAmI()` method on the `a` object. If `a.whoAmI()` is called the method on the `a` object will be referenced rather than `Foo.protoype`.

### Constructor Function vs `Prototype`

- As we have already discovered, in JS we can define methods for classes in one of two ways. Either we can create the method directly in the constructor function or we can add it to the `Prototype`. But what is the difference?

- The key advantage of using the constructor function is that we can take advantage of closure by declaring private variables in the and using them on some method that we create. This probably won't be the case most of the time and therefore its likely that the method is better to be placed on the `Prototype`. The `Prototype` has the advantage of taking up less memory. When an object instance is created everything in the constructor is added to the new Object instance, you can imagine that if hundreds or thousands of instances are created then that object becomes memory intensive. As we learned earlier, if a method is not found on the current `object.prototype`, the JS engine will traverse the prototype chain until the method is found, as a result we use less memory.

- In the example below both approaches have been taken, the private varible `records` is only accessible via methods defined in the constructor. Variables that are publicly available can be accessed using `prototype` methods.

```js
function Person(name, family) {
  this.name = name;
  this.family = family;

  var records = [{ type: 'in', amount: 0 }];

  this.addTransaction = function(trans) {
    if (trans.hasOwnProperty('type') && trans.hasOwnProperty('amount')) {
      records.push(trans);
    }
  };

  this.balance = function() {
    var total = 0;

    records.forEach(function(record) {
      if (record.type === 'in') {
        total += record.amount;
      } else {
        total -= record.amount;
      }
    });

    return total;
  };
}

Person.prototype.getFull = function() {
  return this.name + ' ' + this.family;
};

Person.prototype.getProfile = function() {
  return this.getFull() + ', total balance: ' + this.balance();
};
```
