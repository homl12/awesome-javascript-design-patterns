First of all I love Design patterns readings! The same as reality, the love isn’t always shared, 
also the patterns aren’t always clear at a first sight!
Over the years, trying to write clean and easy maintainable code, 
we start to recognize common problems and here is the main design patterns role:

#### Reusable solution to a commonly occurring problem within a given context in software design.

Last years I’m focused in JavaScript development, so it’s a great opportunity to experience myself with some patterns and their JavaScript equivalent.
Several projects have passed, so now it’s my contribution and sharing experience time!
 I’ll share a high overview of the patterns I used, and these ones which caught my attention, along my JavaScript journey!
Keep in mind that the patterns in the article can be implemented in various ways.
Here are the covered patterns:

 * *For warming up, we’ll start with the base ones and smoothly deep dive into the ocean.*

1. Module
2. Factory
3. Observer
4. Facade
5. Adapter

## Module

JavaScript modules are self-contained code blocks, that perform specific work.

####The main benefit of modules is encapsulation.####

Because of this — the modules don’t pollute the global namespace. Variables, functions and so on are private inside the module body. 
That keeps everything safe inside a module and can’t be overwritten unconsciously outside the module.

**How to define a module:**

Here we define a simple calculation module, that currently supports only sum functionality:

```
// Sum two numbers
export const sum = (a, b) => a + b
```

**How to use the module:**

```
import { sum } from 'path/to/module/'

// 3
const result = sum(1, 2
```

 * *If the module is more complex, we can expand its functionality and even more — split it in different files.*

 * *There are a few more ES6 specific modules features (as export default), but they are out of the article scope, in order to keep it concise.*

## Factory

Design pattern, where object creation is delegated to another object, the later called factory.

#### Factory pattern abstracts the object creation.

```
Let’s say we have Books and Authors classes:

class Book {
  constructor(name, category, author){
    this.name = name
    this.category = category
    this.author = author
  }
}

class Author {
  constructor(name){
    this.name = name
  }
}
```

Now imagine somewhere in our code, called client code, we want to create a new Book.
Without implementing Factory pattern, we would do it in a similar way:

```
// Somewhere in our client code
const author = new Author('Jordan')
const book = new Book('Design patterns', 'Programming', author)
```

What’s the problem here? Good question! Imagine if the creation of Books requires many more related object
creation with a lot of additional parameters . And now imagine you have several places where you’re creating Books in the same way. 
Boom! Now we’re having a complexity, that we have to find out how to manage it.

Here’s how we can handle it with a Factory:

```
const bookFactory = {
  create(bookName, category, authorName) {
    const author = new Author(authorName)
    const book = new Book(bookName, category, author)
    
    return book
  }
}

// Client Code
bookFactory.create('Design patterns', 'Programming', 'Jordan')
```

The benefits of Factory abstraction are:

* Hide and reuse complex objects creation. Otherwise adding a new functionality to the creation process, 
as a new related object for example, will require us to change all Books creations.

* Decoupling Client code from concrete implementations. Without using `bookFactory` , Client code will be always coupled to a concreteBook object.
 Having factory, we can change all `Book` objects easily with another one object.

## Observer

Also known as Publish / Subscribe. It’s an event based pattern where we have two main actors. The first one (known as Publisher) 
publishes events, when something significant happens, and the second one (known as Subscriber) is subscribed 
to the events of the first one and also does some job when an event occurs.

#### Observer pattern promotes loose coupling.

By introducing events in our architecture, we decoupled the Publishers from the Subscribers. 
Doing it that way the Publisher only emits events and it doesn’t know anything about the Subscribers. 
That means that the Publishers and the Subscribers are loosely coupled which is the main benefit. For example:

* We can add / change / remove as many Subscribers as we needed, without modifying the Publisher functionality, which is error less.

Let’s start with the Publisher:

Here the Book class acts as a Publisher. 
On each new Book creation, a new event (*book-created*) is being published to all the Subscribers with the Book object as a data.
The key benefit here is that the Book is not coupled to any Subscriber:

```
class Book {
  constructor(observable, name, author) {
    this.observable = observable
    this.name = name
    this.author = author
    
    this.observable.publish('book-created', {
      name,
      author
    })
  }
}
```

Here the BookStore acts as a Subscriber. The BookStore is subscribed to the Books creation event (via *book-created*) 
and all the created Books are automatically added to the BookStore. Here again, the both actors - Subscriber and Publisher are loosely coupled:

```
class BookStore {
  constructor(observable) {
    this.observable = observable
    this.books = []
    
    this.observable.subscribe('book-created', this.addBook.bind(this))
  }
  addBook(book) {
    this.books.push(book)
  }
}

const observable = new Observable()
const bookStore = new BookStore(observable)
const book = new Book(observable, 'Design patterns', 'Jordan Enev')

// [ { name: 'Design Patterns', author: 'Jordan Enev' } ]
bookStore.books
```

If you are interested in the Observable implementation, you can check it [here](https://gist.github.com/jordan-enev/84cb4c57c2e964947ab1b67211c80a09).
A side note. Last year I’m using React & Redux. While understanding Redux, 
I realized that its architecture and workflow of dispatching actions (publishing events) and updating 
only the affected (subscribed) React components is much similar to the Observer pattern. 
The Observer is really powerful pattern, that you have to consider, if you haven’t done so.

## Facade

The Facade pattern simplifies and hides the complexity of large code blocks or APIs, providing a cleaner,
understandable and easy of use interface.

#### Facade simplifies and hides the complexity

There is a very good real-life example of the pattern — *The car starter engine*. As drivers, we just turn the key on and the car get started. 
As simple as possible. Behind the scenes, many other car systems are involved (as battery, engine, fuel, etc.), 
in order the car to start successfully, but they are hidden behind the starter.

As you can see, the car starter is the Facade. It gives us easy to use interface, without worrying about the complexity of all other car systems.

As a code example I’ll mention a concrete use-case from the current project I’m participating — *Tables creation*

 * *The example will use JSX for the sake of clearness code and it doesn’t relate to ES6. 
 Don’t worry if you don’t have any experience with it. You can follow the code as a pseudo one. 
 You can keep the focus on the ES6 methods and techniques used in the example as `Set, Array.reduce, Array.map` , Arrow functions and Destructuring assignment.*

 Maybe you’re asking yourself what’s the complexity here, but let’s review the requirements:

1. Each *Table* is composed by *Heading* and *Body* components.
2. In order to use the *Table*, we have to pass a *data* array parameter.
3. According to the *data* values, the Table *headings* and *rows* have to be built dynamically. Example:
If we pass `const data = [ { name: 'Jordan', country: 'Bulgaria' }]`, then the Table’s headings columns have be respectively *name* and *country*.

Here’s our *Table* implementation. As you can see below the *Table* component acts as Facade. 
It hides the complexity of building heading and rows, thus their composition and provide us clean and easy to use interface.

```
export const Heading = ({ headings }) => {
  return <thead>
    <tr>
      { headings.map(heading => <th>{ heading }</th>)}
    </tr>
  </thead>
}

export const Body = ({ rows }) => {
  return <tbody>
    { rows.map(row => <tr>
      <td> { row.map(td => td) } </td>
    </tr>)
  }
  </tbody>
}

// Table Facade.
// We just need to pass the `data`,
// and the Facade will hide all the internal complexity.
export const Table = ({ data }) => {
  const headings = buildHeadings(data)
  const rows = buildRows(data, headings)

  return <table>
    <Heading headings={headings} />
    <Body rows={rows} />
  </table>
}
```

So In order to use the Table component, we just need to pass the *data* as follows:

```
const data = [
  { name: 'Jordan Enev', email: 'jordan@devlabs.bg', country: 'Bulgaria'},
  { name: 'Nadezhda Serafimova', email: 'nadezhda@devlabs.bg', country: 'Bulgaria'},
  { name: 'Hristo Eftimov', email: 'hristo@devlabs.bg', country: 'Bulgaria'}
]

<Table data={data} />
```

Now imagine if we don’t have a such *Table Facade*, what we would do? 
Would we call all the Table internals as [buildHeadings, buildRows](https://gist.github.com/jordan-enev/4e63529a4144629b01d2197784f0b4a0) 
and always to compose the Table layout with Heading and Body components when we just want to use the Table? 
Of course we won’t! Facade to the rescue!
Comparing the Table example with the Car example, here the Table acts as the Car starter engine. 
We just pass the data / turn on the key — and all the complexity is hidden from us.
I know that the example is quite simple, but just imagine if we add searching functionality, sorting mechanism, etc.

## Adapter

#### Adapter pattern converts the interface of a class into another interface, which the client code expects.

Here again we have a really, really good real-life example: *Power Plug Adapter*.

As you know, each country follows specific electrical standard (voltage differences, pins sizes, etc).
Let’s say you’re living in a country, where the country’s standard requires the power plugs to have 2 pins. 
Everything is fine. All of yours electrical devices’ plugs are with 2 pins. Now you have to visit England, where the standard plugs have 3 pins. 
Therefore your laptop’s power plug pins will be incompatible with the England’s sockets. The solution is using an Adapter.

Comparing the real-life example with the programming point of view, the laptop’s power plug is the client code, 
which wants to use the electricity, but because of the incompatible interfaces, we firstly need an Adapter, that will convert the electricity interface into another one, the client expects.

Last time I experienced myself with the Adapter pattern, the case was as follows:

I had to create a Chrome extension, for listing daily news, but the problem was that the back-end API returns all the data attached to the one-letter fields. 
For example:

```
[{
  a: 'The title',
  b: 'Description',
  c: '1527517117878',
  d: 'Category' 
}]
```

As you can see it would be very confusing If I used this naming convention in the codebase, 
so I decided to adapt the API response to a more meaningful and understandable structure. 
What I did it is just write a class that converts the above API response structure, to the following one:

```
[{
  title: 'The title',
  description: 'Description',
  time: '1527517117878',
  category: 'Category' 
}]
```

Here’s the code example of the Adapter:

```
// Adapt response (array of objects) object keys, according to the mapping
const responseAdapter = (response, mapping) => {
  return response.map(item => {
    const normalized = {}
    
    // Normalize each response's item key, according to the mapping
    Object.keys(item).forEach(key => (
      normalized[mapping[key]] = item[key]))
    return normalized
  })
}

// API Response
const response = [{
  a: 'The title',
  b: 'Description',
  c: '1527517117878',
  d: 'Category'
}]

// How to Adapt (normalize) the API Response
const mapping = {
  a: 'title',
  b: 'description',
  c: 'time',
  d: 'category'
}

// Usage
responseAdapter(response, mapping)
```

## Conclusion

Doubtless, knowing and understanding the patterns is a good programming knowledge — it will help us to recognize
common problems better and will improve the communication between developers (it’s easily to say — hey why don’t you implement Facade here, 
instead of describing the pattern intent and implementation again and again).

**But please don’t get me wrong!** I intentionally wrote *knowing* and *understanding*, instead of always using them.

It’s fine to **not** start with the design pattern approach firstly, 
when we’re developing something new and we still don’t know the feature’s pitfalls and specifics deeply.

It’s perfectly fine and better to keep the things simple and easy to refactor, 
unless we have enough understanding about the problem we’re trying to resolve. 
Once we have most of the details, then we can think for a pattern or implementation that will fit to our case. 
Now it’s more probable to build a better solution (knowing the details), instead of predicting the future in the beginning of the feature.

While experiencing the patterns with JavaScript, I learnt that the good practices and patterns don’t rely on a specific programming language.
It depends on you, on your passion and the decisions you take.

So the story of my journey ends here. I hope you take some inspiration.
Till to next time ;)






