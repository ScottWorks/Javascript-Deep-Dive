## Context

- Scope is generally handled in one of two ways; either your references are based on rules defined by **Lexical Scope** or they are handled in a "Dynamic" way. In the latter case, Dynamic Scope does not actually exist in JS, instead there is the concept of context (similar to Dynamic Scope) where access to variables and functions is handled via the **`this`** keyword.

### `this` keyword

- So what exactly is the `this` keyword? We have seen it plastered all over our constructor functions and prototype methods. The `this` keyword is a pointer to the specific object instance that is referencing either a property or method of an Object. Take for example the code below:

```js
function Room(room, floor, isClean) {
  this.room = room;
  this.floor = floor;
  this.isClean = isClean;
  this.occupants = 0;
}

Room.prototype.clean = function() {
  this.isClean = true;
};

Room.prototype.addOccupants = function(num) {
  this.occupants += num;
};

Room.prototype.removeOccupants = function() {
  this.occupants = 0;
};

var myOffice = new Room(1, 27, false),
  yourOffice = new Room(4, 12, true);

console.log(myOffice);
// Room { room: 1, floor: 27, isClean: false, occupants: 0 }
console.log(yourOffice);
// Room { room: 4, floor: 12, isClean: true, occupants: 0 }

myOffice.clean();
myOffice.addOccupants(3);

console.log(myOffice);
// Room { room: 1, floor: 27, isClean: true, occupants: 3 }
console.log(yourOffice);
// Room { room: 4, floor: 12, isClean: true, occupants: 0 }

yourOffice.addOccupants(5);

console.log(myOffice);
// Room { room: 1, floor: 27, isClean: true, occupants: 3 }
console.log(yourOffice);
// Room { room: 4, floor: 12, isClean: true, occupants: 5 }
```

- As you can see I decided to use an example that will soon be relevant, basically we have object instances of `Room`s in a building. Each room shares the same properties and methods of the `Room` object. When `myOffice.clean()` is called the `clean()` method knows it needs to reference its the object instance `myOffice` when changing the `this.isClean` property from false to true. Notice how this has nothing to do with Lexical scope in the sense that I am able to reference values without worrying about where functions or properties are declared but rather how they are declared.

### Lexical Scope vs `Object.prototype`

- As we mentioned earlier the usage of the `this` keyword is a similar concept to Dynamic Scope in the sense that references are established based on the call-site rather than author-time variable and function placement. We can see through our previous example how the `Prototype` chain is traveresed to find the context related to the `this` keyword.

- "`Prototype` mechanism is an internal link that exists on one object which references another object. This linkage is exercised when a property/method reference is made against the first object, and no such property/method exists. In that case, the `Prototype` linkage tells the engine to look for the property/method on the linked-to object. In turn, if that object cannot fulfill the look-up, its `Prototype` is followed, and so on. This series of links between objects forms what is called the "prototype chain". In other words, the actual mechanism, the essence of what's important to the functionality we can leverage in JavaScript, is all about objects being linked to other objects.", [source](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch6.md#chapter-6-behavior-delegation).

![fig2](/Part-2-Context-Objects-Prototypes/images/fig2.png)

### Leixcal Scope vs `Context` (`this` keyword)

|                                              | Lexical Scope                     | Context (`this` keyword)     |
| -------------------------------------------- | --------------------------------- | ---------------------------- |
| variable/ function references are created at | compile time                      | runtime                      |
| references based on                          | placement of variables/ functions | global or context object     |
| access control mechanisms                    | closures, hoisting                | binding, inheritence, mixins |
| considered to be                             | predictable                       | flexible                     |
