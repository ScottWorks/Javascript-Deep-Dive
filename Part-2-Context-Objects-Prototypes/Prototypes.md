## Prototypes

- Objects in JavaScript use an interna property known as `[[ProtoType]]` as a means of referencing one object to another. Generally objects have a non-null `[[ProtoType]]` value upon creation.

```js
var myObject = {
  a: 2
};

myObject.a; // 2
```
