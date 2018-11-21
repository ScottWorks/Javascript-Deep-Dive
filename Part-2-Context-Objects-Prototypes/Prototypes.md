## Prototypes

- Objects in JavaScript use an internal property known as `[[ProtoType]]` as a means of referencing one object to another. Generally objects have a non-null `[[ProtoType]]` value upon creation.

```js
var myObject = {
  a: 2
};

myObject.a; // 2
```

- As we mentioned in the [Objects](Part-2-Context-Objects-Prototypes/Objects.md) section when we reference a property on an Object the `[[GET]]` operation checks the object itself for said property. If the property is not found we begin to traverse the `[[ProtoType]]` chain until it is located. This may look like the following example, where we 'copy' an object and reference a property from the copy. Clearly we never actually define the specific property being referenced however it can be located through the `[[ProtoType]]` chain. It should be mentioned that if the property did not exist on the copied object then an `undefined` would be returned.

```js
var anotherObject = {
  a: 2
};

// create an object linked to `anotherObject`
var myObject = Object.create(anotherObject);

myObject.a; // 2
myObject.b; // undefined
```

#### `Object.prototype`

- Every `[[ProtoType]]` chain is built on top of the `Object.prototype` built-in, reason being that all objects in JavaScript descend from the `Object.prototype` object. This provides us with access to utilities such as `.toString()`, `isPrototypeOf()`, and [more](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/prototype)

### Class vs `[[ProtoType]]`

- In traditional class-oritented programming languages creating a new instance of a class is analogous to using a blueprint for building identical copies of a home. Each new home that is created is its own unique instance and this process can be repeated as many times as needed. In JS however we do not follow this same process when creating creating new obhects. Instead we create multiple objects that are **linked** via the `[[ProtoType]]` chain to a common parent object. Therefor for each object instance the properties and methods from the parent are not actually being transferred to the child, rather the child references the parent properties and methods through the `[[ProtoType]]` chain.

- Some common areas people get get tripped up on are generally related to the similarities in syntax that JS shares with other class oriented languages. These similarties include:

  1. usage of the `new` keyword
  2. defining classes with capital letters (`Class Car()`)
  3. the constructor appears to be part of a specific object then the built-in `Object.prototype`:

  ```js
  function Foo() {
    /* .. */
  }

  Foo.prototype = {
    /* .. */
  }; // create a new prototype object

  var a1 = new Foo();
  a1.constructor === Foo; // false!
  a1.constructor === Object; // true!
  ```

#### `Object.prototype.constructor`

- In the code snippet above we may interpret this as object `a` having its own `.constructor` property. This is not the case however in this example as we have taken the liberty of , when `a` is linked to `Foo` (which is linked to `Object.prototype`) via the `[[ProtoType]]` chain `a` gains access to the `.constructor` (which is property of the [`Object.prototype`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/constructor). To clear things up a bit when `a.constructor` is called, the following happens:

  - `[[ProtoType]]` checks `a` object, constructor is not found
  - `[[ProtoType]]` checks `Foo.prototype` object, constructor is not found
  - `[[ProtoType]]` checks `Object.prototype` object, constructor is found!

- If we did not overwrite the `Foo.prototype`, the `[[ProtoType]]` chain would find `.constructor` at the `Foo.prototype`. Therefore `a1.constructor === Foo;` would return `true`.
