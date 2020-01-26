# Understanding Design Patterns in JavaScript

When you start a new project, you don’t immediately start coding. 
You first have to define the purpose and scope of the project, 
then list out the project features or specs. 
After you can either start coding or if you are
 working on a more complex project then you should choose a design pattern that best suits your project.

# What is a Design Pattern?

In software engineering, a design pattern is a reusable solution for commonly occurring problems in software design. 
Design patterns represent the best practices used by the experienced software developers. A design pattern can be thought of as a programming template.

# Why use Design Patterns?

Many programmers either think design patterns are a waste of time or they don’t know how to apply them appropriately. 
But using an appropriate design pattern can help you to write better and more understandable code, and the code can be easily maintained because it’s easier to understand.
Most importantly, 
the design patterns give software developers a common vocabulary to talk about. They show the intent of your code instantly to someone learning the code.
For example, if you are using decorator pattern in your project, then a new programmer would 
immediately know what that piece of code is doing, and they can focus more on solving the business problem rather than trying to understand what that code is doing.
Now that we know what design patterns are, and why they are important, let’s dive into various design patterns used in JavaScript.
# Module Pattern

A Module is a piece of self-contained code so we can update the Module without affecting the other parts of the code. 
Modules also allow us to avoid namespace pollution by creating a separate scope for our variables. 
We can also reuse modules in other projects when they are decoupled from other pieces of code.
Modules are an integral part of any modern JavaScript application and help in keeping our code clean, 
separated and organized. There are many ways to create modules in JavaScript, 
one of which is Module pattern.

Tip: Use [Bit](https://github.com/teambit/bit) to build faster with components. 
It helps your team share and reuse components, and use them to build new applications. Give it a try.

Unlike other programming languages, JavaScript doesn’t have access modifiers, that is, you can’t declare a variable as private or public. 
So the Module pattern is also used to emulate the concept of encapsulation.
This pattern uses IIFE (immediately-invoked function expression), closures and function scope to simulate this concept. For example:

```
const myModule = (function() {
  
  const privateVariable = 'Hello World';
  
  function privateMethod() {
    console.log(privateVariable);
  }
  return {
    publicMethod: function() {
      privateMethod();
    }
  }
})();
myModule.publicMethod();
```

As it’s IIFE, the code is immediately executed, and the returned object is assigned to the `myModule` variable. 
Due to closures, the returned object can still access the functions and variables defined inside the IIFE even after when IIFE has finished.
So the variables and functions defined inside the IIFE are essentially hidden from the outer scope and thus making it private to the `myModule` variable.
After the code is executed, the `myModule` variable looks like this:

```
const myModule = {
  publicMethod: function() {
    privateMethod();
  }};
  ```
So we can call the `publicMethod()` which will, in turn, call the `privateMethod()`. For example:

```
// Prints 'Hello World'
module.publicMethod();
```

# Revealing Module Pattern

The Revealing Module pattern is a slightly improved version of the module pattern by Christian Heilmann. 
The problem with the module pattern is that we have to create new public functions just to call the private functions and variables.

In this pattern, we map the returned object’s properties to the private functions that we want to reveal as public. 
That’s why it’s called Revealing Module pattern. For example:

```
const myRevealingModule = (function() {
  
  let privateVar = 'Peter';
  const publicVar  = 'Hello World';
  function privateFunction() {
    console.log('Name: '+ privateVar);
  }
  
  function publicSetName(name) {
    privateVar = name;
  }
  function publicGetName() {
    privateFunction();
  }
  /** reveal methods and variables by assigning them to object     properties */
return {
    setName: publicSetName,
    greeting: publicVar,
    getName: publicGetName
  };
})();
myRevealingModule.setName('Mark');
// prints Name: Mark
myRevealingModule.getName();
```

This pattern makes it easier to understand which of our functions and variables can be accessed publicly, which helps in code readability.
After the code is executed, the `myRevealingModule` looks like this:

```
const myRevealingModule = {
  setName: publicSetName,
  greeting: publicVar,
  getName: publicGetName
};
```

We can call `myRevealingModule.setName('Mark')`, which is a reference to the inner `publicSetName` and `myRevealingModule.getName()`, which is a reference to the inner `publicGetName`. 
For example:

```
myRevealingModule.setName('Mark');
// prints Name: Mark
myRevealingModule.getName();

```
# Advantages of Revealing Module pattern over Module Pattern:

* We can change members from public to private and vice versa by modifying a single line in the return statement.
* The returned object contains no function definitions, all right-hand side expressions are defined inside the IIFE, making the code clear and easy to read.
# ES6 Modules

Before ES6, JavaScript didn’t have built-in modules, so developers had to rely on third-party 
libraries or the module pattern to implement modules. But with ES6, JavaScript has native modules.
ES6 modules are stored in files. There can only be one module per file. 
Everything inside a module is private by default. 
Functions, variables, and classes are exposed using the `export` keyword. 
The code inside a module always runs in strict mode.
# Exporting a Module
There are two ways to export a function and variable declaration:
* By adding the `export` keyword in front of function and variable declaration. For example:

```
// utils.js
export const greeting = 'Hello World';
export function sum(num1, num2) {
  console.log('Sum:', num1, num2);
  return num1 + num2;
}
export function subtract(num1, num2) {
  console.log('Subtract:', num1, num2);
  return num1 - num2;
}
// This is a private function
function privateLog() {
  console.log('Private Function');
}

```

By adding the `export` keyword at end of the code containing names of functions and variables we want to export. For example:

```
// utils.js
function multiply(num1, num2) {
  console.log('Multiply:', num1, num2);
  return num1 * num2;
}
function divide(num1, num2) {
  console.log('Divide:', num1, num2);
  return num1 / num2;
}
// This is a private function
function privateLog() {
  console.log('Private Function');
}
export {multiply, divide};
```

# Importing a Module
Similar to exporting a module, there are two ways to `import` a module by using the import keyword. For example:
* **Importing multiple items at one time**

```
// main.js
// importing multiple items
import { sum, multiply } from './utils.js';
console.log(sum(3, 7));
console.log(multiply(3, 7));
```

* **Importing all of a module**

```
// main.js
// importing all of module
import * as utils from './utils.js';
console.log(utils.sum(3, 7));
console.log(utils.multiply(3, 7));
```

# Imports and Exports can be aliased
If you want to avoid naming collisions, you can change the name of export both during export as well as import. For example:

* **Renaming an export**

```
// utils.js
function sum(num1, num2) {
  console.log('Sum:', num1, num2);
  return num1 + num2;
}
function multiply(num1, num2) {
  console.log('Multiply:', num1, num2);
  return num1 * num2;
}
export {sum as add, multiply};
```

* **Renaming an import**
```
// main.js
import { add, multiply as mult } from './utils.js';
console.log(add(3, 7));
console.log(mult(3, 7));
```
# Singleton Pattern
A Singleton is an object which can only be instantiated only once. 
A singleton pattern creates a new instance of a class if one doesn’t exist. 
If an instance exists, it simply returns a reference to that object. 
Any repeated calls to the constructor would always fetch the same object.
JavaScript has always had singletons built-in to the language. 
We just don’t call them singletons, we call them object literal. For example:

```
const user = {
  name: 'Peter',
  age: 25,
  job: 'Teacher',
  greet: function() {
    console.log('Hello!');
  }
};
```
Because each object in JavaScript occupies a unique memory 
location and when we call the `user` object, we are essentially returning reference to this object.
If we try to copy the `user` variable into another variable and modify that variable. For example:

```
const user1 = user;
user1.name = 'Mark';
```

We would see both of objects are modified because objects in JavaScript are passed by reference not by value. 
So there is only a single object in the memory. For example:

```
// prints 'Mark'
console.log(user.name);
// prints 'Mark'
console.log(user1.name);
// prints true
console.log(user === user1);
```

Singleton pattern can be implemented using the constructor function. For example:

```
let instance = null;
function User() {
  if(instance) {
    return instance;
  }
  instance = this;
  this.name = 'Peter';
  this.age = 25;
  
  return instance;
}
const user1 = new User();
const user2 = new User();
// prints true
console.log(user1 === user2); 
```
When this constructor function is called, it checks if the `instance` object exists or not. 
If the object doesn’t exist, it assigns the `this` variable to the `instance` variable. And if the object exists, it just returns that object.
Singletons can also be implemented using the module pattern. For example:

```
const singleton = (function() {
  let instance;
  
  function init() {
    return {
      name: 'Peter',
      age: 24,
    };
  }
  return {
    getInstance: function() {
      if(!instance) {
        instance = init();
      }
      
      return instance;
    }
  }
})();
const instanceA = singleton.getInstance();
const instanceB = singleton.getInstance();
// prints true
console.log(instanceA === instanceB);
```

In the above code, we are creating a new instance by calling the `singleton.getInstance` method. 
If an instance already exists, this method simply returns that instance, 
if the instance doesn’t exist, it creates a new instance by calling the `init()` function.
# Factory Pattern
Factory Pattern is a pattern that uses factory methods to create objects 
without specifying the exact class or constructor function from which the object will be created.
The factory pattern is used to create objects without exposing the instantiation logic. 
This pattern can be used when we need to generate a different 
object depending upon a specific condition. For example:

```
class Car{
  constructor(options) {
    this.doors = options.doors || 4;
    this.state = options.state || 'brand new';
    this.color = options.color || 'white';
  }
}
class Truck {
  constructor(options) {
    this.doors = options.doors || 4;
    this.state = options.state || 'used';
    this.color = options.color || 'black';
  }
}
class VehicleFactory {
  createVehicle(options) {
    if(options.vehicleType === 'car') {
      return new Car(options);
    } else if(options.vehicleType === 'truck') {
      return new Truck(options);
      }
  }
}
```

Here I have created a `Car` and a `Truck` class (with some default values) which is used to create new `car` and `truck` objects. 
And I have defined a `VehicleFactory` class to create and return a new object based on `vehicleType` property received in the `options` object.

```
const factory = new VehicleFactory();
const car = factory.createVehicle({
  vehicleType: 'car',
  doors: 4,
  color: 'silver',
  state: 'Brand New'
});
const truck= factory.createVehicle({
  vehicleType: 'truck',
  doors: 2,
  color: 'white',
  state: 'used'
});
// Prints Car {doors: 4, state: "Brand New", color: "silver"}
console.log(car);
// Prints Truck {doors: 2, state: "used", color: "white"}
console.log(truck);
```

I have created a new object `factory` of `VehicleFactory` class.
 After that we can create a new Car or Truck object by calling `factory.createVehicle` and passing an `options` object with a vehicleType property with a value of `car` or `truck`.

# Decorator Pattern

A Decorator pattern is used to extend the functionality of an 
object without modifying the existing class or constructor function. 
This pattern can be used to add features to an object without 
modifying the underlying code using them.

A simple example of this pattern would be:

```
function Car(name) {
  this.name = name;
  // Default values
  this.color = 'White';
}
// Creating a new Object to decorate
const tesla= new Car('Tesla Model 3');
// Decorating the object with new functionality
tesla.setColor = function(color) {
  this.color = color;
}
tesla.setPrice = function(price) {
  this.price = price;
}
tesla.setColor('black');
tesla.setPrice(49000);
// prints black
console.log(tesla.color);
```

A more practical example of this pattern would be:
Let’s say, the cost of a car differs depending upon the number of features it has. 
Without decorator pattern, we would have to create different classes for different combinations of features, each having a cost method to calculate the cost. For example:

```
class Car() {
}
class CarWithAC() {
}
class CarWithAutoTransmission {
}
class CarWithPowerLocks {
}
class CarWithACandPowerLocks {
}

But with decorator pattern, we can create a base class `Car` and add the cost of different configuration to its object using the decorator functions. For example:

```
class Car {
  constructor() {
  // Default Cost
  this.cost = function() {
  return 20000;
  }
}
}
// Decorator function
function carWithAC(car) {
  car.hasAC = true;
  const prevCost = car.cost();
  car.cost = function() {
    return prevCost + 500;
  }
}
// Decorator function
function carWithAutoTransmission(car) {
  car.hasAutoTransmission = true;
   const prevCost = car.cost();
  car.cost = function() {
    return prevCost + 2000;
  }
}
// Decorator function
function carWithPowerLocks(car) {
  car.hasPowerLocks = true;
  const prevCost = car.cost();
  car.cost = function() {
    return prevCost + 500;
  }
}
```

First, we create a base class `Car` for creating the `Car` objects. 
Then, we create the decorator for the feature we want to add onto it and pass 
the `Car` object as a parameter. 
Then we override the cost function of that object which returns the 
updated cost of the car and adds a new property to that object to indicate which feature has been added.
To add a new feature, we could do something like this:

```
const car = new Car();
console.log(car.cost());
carWithAC(car);
carWithAutoTransmission(car);
carWithPowerLocks(car);
```

In the end, we can calculate the cost of the car like this:
```
// Calculating total cost of the car
console.log(car.cost());
```

# Conclusion
We have learned about various design patterns used in 
JavaScript, but there are design patterns that I haven’t covered here, which can be implemented in JavaScript.
While it’s important to know various design patterns, 
it’s also equally important to not to overuse them. 
Before using a design pattern, you should carefully consider if your problem fits that design pattern or not. 
To know if a pattern fits your problem, 
you should study the design pattern as well as the applications of that design pattern.
