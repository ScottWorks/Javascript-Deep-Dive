## Classes

### Class Theory

- "Class/Inheritance" describes a certain form of code organization and architecture -- a way of modeling real world problem domains in our software.

- In object or class oriented programming data has information that is directly used but there is also information that can be derived from that particular piece of data through the use of methods or properties. The proper way to handle this is to encapsulate the data and its methods/ properties into one class.

- Lets think about the relationship in software with classes by defining the `Cyclist` and `Athlete` classes. The `Athlete` class would define the generic characteristics (in this context I am referring to characteristics as data/ properties/ and methods) of a Athlete such as energy level, location, strength, stamina, etc. The `Athlete` class may be used to describe a wide range of athletes (racecar drivers, tennis players, etc). When we create the `Cyclist` class is wouldn't make much sense to copy the same characteristics, instead the `Cyclist` class will 'inherit' (or extend) the characteristics defined in `Athlete`. The `Cyclist` class would go on to define characteristics that are specific to a cyclist, i.e. equipment, bike handling skills, mechanic skills, stoke-ness level, etc.

- As you may be able to see we can define complex real world systems with a high level of granularity through the usage of OO/ CO programming. To make this possible more advanced techniques must be employed. **Polymorphism** is the idea that the general behavior from a parent class can be overridden in a child class to give it more specifics. **Relative Polymorphism** allows us to reference the base behavior from the overridden behavior.

### 'Class' as a Design Pattern

- Design patterns such as 'Iterator', 'Observer', 'Factory', 'Singleton', etc all leverage the lower level mechanics of the OO class system. The OO programming subtype is a subset of **Imperative Programming** which is described as [a programming paradigm that uses statements that change a program's state. In much the same way that the imperative mood in natural languages expresses commands, an imperative program consists of commands for the computer to perform](https://en.wikipedia.org/wiki/Imperative_programming). **Procedural Programming** (another subtype) is a way of describing code which only consists of functions which call other functions without any higher abstractions. In complete contrast there is also [**Declarative Programming**](https://en.wikipedia.org/wiki/Declarative_programming) which [**Functional Programming**](https://en.wikipedia.org/wiki/Functional_programming#JavaScript) stems from.

### Javascript "Classes"

- While this section does cover classes at length, it is important to keep in mind that JavaScript does not actually have classes. The syntax is decieving however the low level mechanics of JS operate differenly then languages like Java or C++. It is possible to replicate classes in the classical sense but that comes with a great deal of effort. The purpose of this section is to explore the different pitfalls of the JS class system.

### Class Mechanics

- We can think of classes as a blueprint which describes the design of a building. The blueprints themself are not the whole story though, we must actually construct a building (an object) by following the blueprints. Therefore we use classes to construct (or instantiate) objects, each time we instantiate an object we create an "instance" which is a completely new copy of the class contaainig all of its characteristics. Think of homes that share the same blueprint, if a window gets smashed in one home does it occur to all the others? No! of course not each home is its own individual instance of a home, they share the same blueprint but that is all. In the following figure (kindly provided by [Kyle Simpson](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch4.md)) we can see the visual relationship between two classes (foo and bar) and the object instances they produce (Foo.a1, Foo.a2, Foo.b1, Foo.b2). Keep in mind it is better to think of the direct relationship as top-down (class to an object instance) rather than the reverse indirect relationship.

![fig1](/Part-2-Objects-Prototypes/images/fig1.png)

#### Constructor

- As we discussed in the previous section in traditional class-oriented programming languages **Constructor** is a special method attached to a class. The purpose of the constructor method is to initialize any iniformation (state) the instance will need. Constructors are generally called with the `new` keyword to signal to the compiler engine that a new class instance should be created.

### Class Inheritance

- As we demonstrated in our earlier example with the `Cyclist`, and `Athlete` classes, it was briefly mentioned that the `Cyclist` class could inherit the characteristics of the `Athlete` class. It's probably no surprise at this point but this concept is called **inheritance**. In this case the `Cyclist` class is a child of the `Athlete` (parent) class. Lets consider the following pseudo-code:

```js
class Athlete {
    weight = 155 // lbs
    stamina = 400;
    endurance = 450;
    strength = 465;
    speed = 0;

    recover(x){
        stamina += x
        endurance += x;
        strength += x;
    }

    workout(x){
        stamina += x
        endurance += x;
        strength += x;
    }

    increaseSpeed(){
        //
    }

    decreaseSpeed(){
        //
    }
}

class Cyclist inherits Athlete {
    bikeType = 'road';
    bikeWeight = 15 // lbs

    wheelie(){
        stamina -= 10
    }

    overTake(){
        inherited: increaseSpeed()

        stamina -= 20
        speed += 3
    }

    draft(){
        endurance += x;
        speed += x;
    }
}

class TrailRunner inherits Athlete {

    jump(){
        //
    }

    balance(){
        //
    }
}
```

#### Polymorphism

- Another thing that was mentioned earlier was the concept of polymorphism, "the idea that the general behavior from a parent class can be overridden in a child class to give it more specifics". In the previous example we saw that the `Cyclist` class defines the function `overTake()` which called `inherited: increaseSpeed()`, this indicates that the class references the parent class's method `increaseSpeed()`. The main take away here is that one method can reference another (while retaining the same name or creating a new one) at a higher level in the inheritance heirarchy.

- Most languages use the keyword `super` instead of our psuedo coded `inherited`, it may be helpful to think of super as a keyword that refers to a "super class" or parent/ancestor of the current class.

- In traditional class-oriented languages the `super` keyword is a direct way for the constructor of a child class to reference the constructor of its parent. Reason being that the constructor is actually a part of the class. This is not the case in JS, it is actually the complete opposite. In JS, think of the class as belonging to the constructor (`Foo.prototype` type references). **In JS the relationship between child and parent exists only between the two `.prototype` objects of the respective constructors**, the constructors themselves are not directly related therefore there is no simple way to relatively reference one from another.
