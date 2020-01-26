 # JavaScript design patterns #1. Singleton and the Module

1. JavaScript design patterns #1. Singleton and the Module
2. [JavaScript design patterns #2. Factories and their implementation in TypeScript](https://wanago.io/2019/12/02/javascript-design-patterns-factories-typescript/)
3. [JavaScript design patterns #3. The Facade pattern and applying it to React Hooks](https://wanago.io/2019/12/09/javascript-design-patterns-facade-react-hooks/)
4. [JavaScript design patterns #4. Decorators and their implementation in TypeScript](https://wanago.io/2019/12/23/javascript-design-patterns-4-decorators-and-their-implementation-in-typescript/)
5. [JavaScript design patterns #5. The Observer pattern with TypeScript](https://wanago.io/2020/01/20/javascript-design-patterns-observer-typescript/)

While having a vibe of a mad genius might be tempting, reinventing the wheel is usually not the best way to approach designing your software.
The chances are that somebody already had the same problem as you and solved it in a smart way. 
Such best practices, when formalized, are called **design patterns**. Today we look into their concept and inspect a **singleton** and a **module**.

## What the design patterns are

We can treat design patterns as proven solutions that many developers tested in various real-life situations. 
They aim to support software designers in solving common problems in a readable and predictable way. 
If we base our application on proven patterns, we can worry less about the overall structure because 
they tend to encourage us to write our code in an organized way.

Looking into an already existing codebase that incorporates one of the design patterns 
might be easier than trying to understand an unfamiliar approach. 

They also act as a bridge between other developers and us. Using well-known strategies makes communication faster and easier.

The design patterns don’t act as exact solutions. They provide us with a scheme that we can adapt to suit our own needs. 
The fact that the patterns are not tied to a specific problem makes them very reusable. 
They are not associated with a particular programming language, but JavaScript has design patterns that are more popular than others.

You probably already use some of them. Common JavaScript solutions tend to have design patterns that feel adequate when implemented. 
React often incorporates the Higher-Order component pattern and the flux architecture.
Angular applications seem to work well when implementing the observer design pattern.

## The Singleton

We start with a design pattern called a **singleton**.
It is one of the most well-known patterns, and therefore it is a good starting point. 
In its core, it restricts a class to have just one instance and ensures that it is globally accessible. 
It might come in handy when you need to manage something from across your whole application.

The term singleton comes [from math](https://en.wikipedia.org/wiki/Singleton_(mathematics)) and means a set with exactly one element

By design, singletons create an instance of a class if it does not yet exist. Otherwise, they return the reference to an existing instance.

```
class Singleton {
  static instance;
  constructor() {
    // your logic here
  }
  static getInstance() {
    if (Singleton.instance) {
      return Singleton.instance;
    }
    Singleton.instance = new Singleton();
    return Singleton.instance;
  }
}
```

Now, every time we call  `Singleton.getInstance()`, we get the same object.

```
Singleton.getInstance() === Singleton.getInstance(); // true
```

The code above looks fine at first glance, but it has some issues. 
Nothing restricts us from calling the Singleton constructor directly. This is when the TypeScript might come in handy.

```
class Singleton {
  private static instance?: Singleton;
  private constructor() {
    // your logic here
  }
  static getInstance() {
    if (Singleton.instance) {
      return Singleton.instance;
    }
    Singleton.instance = new Singleton();
    return Singleton.instance;
  }
}
```

By making the Singleton constructor private, we can only call it from within the  `getInstance` function.

Another approach that we can take is to return an instance straight from within the constructor.

```
class Singleton {
  static instance;
  constructor() {
    if (Singleton.instance) {
      return Singleton.instance;
    }
    Singleton.instance = this;
 
    // your logic here
  }
}
```

```
new Singleton() === new Singleton() // true
```

The above makes it a bit less transparent because someone might not be aware that the constructor returns the same object every time.

Singletons have a lot in common with global variables. This is also why they often are discouraged because they share their disadvantages 
as it might make your application less readable. Whether we consider singletons good or bad, they are one of the fundamental design patterns.
Understanding them might one day come in handy. 
Even if you don’t decide to write them yourself, you might encounter them in some applications.

## The module pattern

Another typical pattern found in JavaScript applications is the module design pattern. 
Separating the code of our app into modules plays a significant role in keeping our codebase neatly organized.

Some time ago, a popular approach would be to enclose a piece of code in an Immediately Invoked Function Expression (IIFE). 
This is because all JavaScript files share the same scope.

**index.html**

```
<!DOCTYPE html>
<html lang="en">
    <head>
        <title>Hello world!</title>
        <script src="hello.js"></script>
        <script src="main.js"></script>
    </head>
    <body>
    </body>
</html>
```

**hello.js**

```
function hello() {
  console.log('Hello world!');
}
 
hello(); // Hello world!
```

**main.js**

```
hello(); // Hello world!
```

The fact that defining something in one file pollutes the whole global scope is not a desirable situation. 
A common way to solve this was to introduce the module pattern by creating a function and immediately invoking it.

**hello.js**

```
(function(){
  function hello() {
    console.log('Hello world!');
  }
 
  hello(); // Hello world!
})();
```

**main.js**

hello(); // Uncaught ReferenceError: hello is not defined
An important thing about the above approach is that if we define any variable inside the above module,
it is not available outside of it.

We can also export the *hello* function by returning something from our Immediately Invoked Function Expression.

**hello.js**

```
const helloModule = (function(){
  function hello() {
    console.log('Hello world!');
  }
 
  return {
    hello
  }
})();
```

**main.js**

```
helloModule.hello(); // Hello world!
```

As the JavaScript language evolved, we found other ways to deal with the above issue. 
One of them are [ES6 modules](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import), where every module has its file. 
Modern browsers already support them. You can also [use them with Webpack](https://wanago.io/2018/07/16/webpack-4-course-part-one-entry-output-and-es6-modules/).

Node.js environment also provides its solution by implementing a module system called CommonJS. 
Let’s examine an odd piece of code:

```
console.log('Hello');
return;
console.log('world!');
```

It seems weird because the **return** statement can’t occur outside of a function. When we import such a file, Node.js wraps it in a function like this:

```
function (exports, require, module, __filename, __dirname) {
  console.log('Hello');
  return;
  console.log('world!');
}
```

Thanks to the above, the module has its own scope, and the above code runs without errors.

If you want to know more, check out [Node.js TypeScript #1. Modules, process arguments, basics of the File System](https://wanago.io/2019/02/11/node-js-typescript-modules-file-system/)

## Summary

In this article, we’ve learned what a design pattern is. The first approaches that we’ve got to know are the singleton and the module. 
A point of the above article is also the fact that you are probably already using design patterns. 
It might be a good idea to go and learn more and expand our programming vocabulary.
Thanks to that, we can come up with a solution to our problem faster. Also, it will probably be more efficient and readable.

Series Navigation
[JavaScript design patterns #2. Factories and their implementation in TypeScript >>](https://wanago.io/2019/12/02/javascript-design-patterns-factories-typescript/)
