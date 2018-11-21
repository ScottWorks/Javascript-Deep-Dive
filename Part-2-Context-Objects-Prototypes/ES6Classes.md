### ES6 classes

- In ES6 `class`es were introduced in the form of syntactic sugar, at first glance it would appear that this is something entirely new as it resembles classes in traditional programming languages however this is not the case. The `class` system is actually a very complicated set of abstractions built on top of the `[[ProtoType]]` mechanism. Although I did not dive into the problems associated with the this mechanism you can read more about them [here](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch6.md). In this section we will discuss the problems that the JS `class` system has that way we can make better decisions while using it.

### `class` Pitfalls

1. Since `[[ProtoType]]` linkages are still being used under the hood of `class` that means that 'classes' are still only referenced objects conneted via links and not copies instances. Hence it is possible to change methods/ properties in a parent function that affect its complementary child even after the child has been "instantiated".

```js
class C {
  constructor() {
    this.num = Math.random();
  }
  rand() {
    console.log('Random: ' + this.num);
  }
}

var c1 = new C();
c1.rand(); // "Random: 0.4324299..."

C.prototype.rand = function() {
  console.log('Random: ' + Math.round(this.num * 1000));
};

var c2 = new C();
c2.rand(); // "Random: 867"

c1.rand(); // "Random: 432" -- oops!!!
```

2. There is no way to declare class memeber properties once a class has been declared. We can only declare methods as so:

```js
someClass.prototype.someMethod = function() {
  console.log('Hey im someMethod, on someClass!');
};
```

- This is problematic when creating variables that keep track of shared state between class instances. The only way to do this is to leverage the `.prototype` syntax as so.

```js
class C {
  constructor() {
    // make sure to modify the shared state,
    // not set a shadowed property on the
    // instances!
    C.prototype.count++;

    // here, `this.count` works as expected
    // via delegation
    console.log('Hello: ' + this.count);
  }
}

// add a property for shared state directly to
// prototype object
C.prototype.count = 0;

var c1 = new C();
// Hello: 1

var c2 = new C();
// Hello: 2

c1.count === 2; // true
c1.count === c2.count; // true
```

- It should be noted that `this.count++` implicitly creates a [shadowed](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch5.md#setting--shadowing-properties) `.count` property on both `c1` and `c2` objects rather than updating the shared state.

### `class` Usage
