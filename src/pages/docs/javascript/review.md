---
title: Javascript Concepts Review
weight: 0
excerpt: Javascript Concepts Review
seo:
    title: ''
    description: ''
    robots: []
    extra: []
template: docs
---

## Core Concept Review

# Core Concepts

# index

This appendix is a non-exhaustive list of new syntactic features and methods that were added to JavaScript in ES6. These features are the most commonly used and most helpful.

While this appendix doesn't cover ES6 classes, we go over the basics while learning about components in the book. In addition, this appendix doesn't include descriptions of some larger new features like promises and generators. If you'd like more info on those or on any topic below, we encourage you to reference the [Mozilla Developer Network's website](https://developer.mozilla.org/) (MDN).

## Prefer `const` and `let` over `var`

If you've worked with ES5 JavaScript before, you're likely used to seeing variables declared with `var`:

```js
ar myVariable = 5;
```

Both the `const` and `let` statements also declare variables. They were introduced in ES6.

Use `const` in cases where a variable is never re-assigned. Using `const` makes this clear to whoever is reading your code. It refers to the "constant" state of the variable in the context it is defined within.

If the variable will be re-assigned, use `let`.

We encourage the use of `const` and `let` instead of `var`. In addition to the restriction introduced by `const`, both `const` and `let` are _block scoped_ as opposed to _function scoped_. This scoping can help avoid unexpected bugs.

## Arrow functions

There are three ways to write arrow function bodies. For the examples below, let's say we have an array of city objects:

```js
onst cities = [
  { name: 'Cairo', pop: 7764700 },
  { name: 'Lagos', pop: 8029200 },
];
```

If we write an arrow function that spans multiple lines, we must use braces to delimit the function body like this:

```js
const formattedPopulations = cities.map((city) => {
    const popMM = (city.pop / 1000000).toFixed(2);
    return popMM + ' million';
});
console.log(formattedPopulations);
```

Note that we must also explicitly specify a `return` for the function.

However, if we write a function body that is only a single line (or single expression) we can use parentheses to delimit it:

```js
const formattedPopulations2 = cities.map((city) => (city.pop / 1000000).toFixed(2) + ' million');
```

Notably, we don't use `return` as it's implied.

Furthermore, if your function body is terse you can write it like so:

```js
const pops = cities.map((city) => city.pop);
console.log(pops);
```

The terseness of arrow functions is one of two reasons that we use them. Compare the one-liner above to this:

```js
const popsNoArrow = cities.map(function (city) {
    return city.pop;
});
```

Of greater benefit, though, is how arrow functions bind the `this` object.

The traditional JavaScript function declaration syntax (`function () {}`) will bind `this` in anonymous functions to the global object. To illustrate the confusion this causes, consider the following example:

```js
unction printSong() {
  console.log("Oops - The Global Object");
}

const jukebox = {
  songs: [
    {
      title: "Wanna Be Startin' Somethin'",
      artist: "Michael Jackson",
    },
    {
      title: "Superstar",
      artist: "Madonna",
    },
  ],
  printSong: function (song) {
    console.log(song.title + " - " + song.artist);
  },
  printSongs: function () {

    this.songs.forEach(function(song) {

      this.printSong(song);
    });
  },
}

jukebox.printSongs();
```

The method `printSongs()` iterates over `this.songs` with `forEach()`. In this context, `this` is bound to the object (`jukebox`) as expected. However, the anonymous function passed to `forEach()` binds its internal `this` to the global object. As such, `this.printSong(song)` calls the function declared at the top of the example, _not_ the method on `jukebox`.

JavaScript developers have traditionally used workarounds for this behavior, but arrow functions solve the problem by **capturing the `this` value of the enclosing context**. Using an arrow function for `printSongs()` has the expected result:

```js
  printSongs: function () {
    this.songs.forEach((song) => {

      this.printSong(song);
    });
  },
}

jukebox.printSongs();
```

For this reason, throughout the book we use arrow functions for all anonymous functions.

## Modules

ES6 formally supports modules using the `import`/`export` syntax.

**Named exports**

Inside any file, you can use `export` to specify a variable the module should expose. Here's an example of a file that exports two functions:

```js
export const sayHi = () => console.log('Hi!');
export const sayBye = () => console.log('Bye!');

const saySomething = () => console.log('Something!');
```

Now, anywhere we wanted to use these functions we could use `import`. We need to specify which functions we want to import. A common way of doing this is using ES6's destructuring assignment syntax to list them out like this:

```js
import { sayHi, sayBye } from './greetings';

sayHi();
sayBye();
```

Importantly, the function that was _not_ exported (`saySomething`) is unavailable outside of the module.

Also note that we supply a **relative path** to `from`, indicating that the ES6 module is a local file as opposed to an npm package.

Instead of inserting an `export` before each variable you'd like to export, you can use this syntax to list off all the exposed variables in one area:

```js
const sayHi = () => console.log('Hi!');
const sayBye = () => console.log('Bye!');

const saySomething = () => console.log('Something!');

export { sayHi, sayBye };
```

We can also specify that we'd like to import all of a module's functionality underneath a given namespace with the `import * as <Namespace>` syntax:

```js
import * as Greetings from './greetings';

Greetings.sayHi();

Greetings.sayBye();

Greetings.saySomething();
```

**Default export**

The other type of export is a default export. A module can only contain one default export:

```js
const sayHi = () => console.log('Hi!');
const sayBye = () => console.log('Bye!');

const saySomething = () => console.log('Something!');

const Greetings = { sayHi, sayBye };

export default Greetings;
```

This is a common pattern for libraries. It means you can easily import the library wholesale without specifying what individual functions you want:

```js
import Greetings from './greetings';

Greetings.sayHi();
Greetings.sayBye();
```

It's not uncommon for a module to use a mix of both named exports and default exports. For instance, with `react-dom`, you can import `ReactDOM` (a default export) like this:

```js
import ReactDOM from 'react-dom';

ReactDOM.render();
```

Or, if you're only going to use the `render()` function, you can import the named `render()` function like this:

```js
import { render } from 'react-dom';

render();
```

To achieve this flexibility, the export implementation for `react-dom` looks something like this:

```js
export const render = (component, target) => {};

const ReactDOM = {
    render
};

export default ReactDOM;
```

If you want to play around with the module syntax, check out the folder `code/webpack/es6-modules`.

For more reading on ES6 modules, see this article from Mozilla: "[ES6 in Depth: Modules](https://hacks.mozilla.org/2015/08/es6-in-depth-modules/)".

## `Object.assign()`

We use `Object.assign()` often throughout the book. We use it in areas where we want to create a modified version of an existing object.

`Object.assign()` accepts any number of objects as arguments. When the function receives two arguments, it _copies_ the properties of the second object onto the first, like so:

```js
onst coffee = { };
const noCream = { cream: false };
const noMilk = { milk: false };
Object.assign(coffee, noCream);
```

It is idiomatic to pass in three arguments to `Object.assign()`. The first argument is a new JavaScript object, the one that `Object.assign()` will ultimately return. The second is the object whose properties we'd like to build off of. The last is the changes we'd like to apply:

```js
const coffeeWithMilk = Object.assign({}, coffee, { milk: true });
```

`Object.assign()` is a handy method for working with "immutable" JavaScript objects.

## Template literals

In ES5 JavaScript, you'd interpolate variables into strings like this:

```js
var greeting = 'Hello, ' + user + '! It is ' + degF + ' degrees outside.';
```

With ES6 template literals, we can create the same string like this:

```js
const greeting = `Hello, ${user}! It is ${degF} degrees outside.`;
```

## The spread operator (`...`)

In arrays, the ellipsis `...` operator will _expand_ the array that follows into the parent array. The spread operator enables us to succinctly construct new arrays as a composite of existing arrays.

Here is an example:

```js
onst a = [ 1, 2, 3 ];
const b = [ 4, 5, 6 ];
const c = [ ...a, ...b, 7, 8, 9 ];

console.log(c);
```

Notice how this is different than if we wrote:

```js
const d = [a, b, 7, 8, 9];
console.log(d);
```

## Enhanced object literals

In ES5, all objects were required to have explicit key and value declarations:

```js
const explicit = {
    getState: getState,
    dispatch: dispatch
};
```

In ES6, you can use this terser syntax whenever the property name and variable name are the same:

```js
const implicit = {
    getState,
    dispatch
};
```

Lots of open source libraries use this syntax, so it's good to be familiar with it. But whether you use it in your own code is a matter of stylistic preference.

## Default arguments

With ES6, you can specify a default value for an argument in the case that it is `undefined` when the function is called.

This:

```js
unction divide(a, b) {

  const divisor = typeof b === 'undefined' ? 1 : b;

  return a / divisor;
}
```

Can be written as this:

```js
function divide(a, b = 1) {
    return a / b;
}
```

In both cases, using the function looks like this:

```js
divide(14, 2);

divide(14, undefined);

divide(14);
```

Whenever the argument `b` in the example above is `undefined`, the default argument is used. Note that `null` will not use the default argument:

```js
divide(14, null);
```

## Destructuring assignments

### For arrays

In ES5, extracting and assigning multiple elements from an array looked like this:

```js
ar fruits = [ 'apples', 'bananas', 'oranges' ];
var fruit1 = fruits[0];
var fruit2 = fruits[1];
```

In ES6, we can use the destructuring syntax to accomplish the same task like this:

```js
const [veg1, veg2] = ['asparagus', 'broccoli', 'onion'];
console.log(veg1);
console.log(veg2);
```

The variables in the array on the left are "matched" and assigned to the corresponding elements in the array on the right. Note that `'onion'` is ignored and has no variable bound to it.

### For objects

We can do something similar for extracting object properties into variables:

```js
const smoothie = {
    fats: ['avocado', 'peanut butter', 'greek yogurt'],
    liquids: ['almond milk'],
    greens: ['spinach'],
    fruits: ['blueberry', 'banana']
};

const { liquids, fruits } = smoothie;

console.log(liquids);
console.log(fruits);
```

### Parameter context matching

We can use these same principles to bind arguments inside a function to properties of an object supplied as an argument:

```js
const containsSpinach = ({ greens }) => {
    if (greens.find((g) => g === 'spinach')) {
        return true;
    } else {
        return false;
    }
};

containsSpinach(smoothie);
```

We do this often with functional React components:

```js
const IngredientList = ({ ingredients, onClick }) => (
    <ul className="IngredientList">
        {ingredients.map((i) => (
            <li key={i.id} onClick={() => onClick(i.id)} className="item">
                {i.name}
            </li>
        ))}
    </ul>
);
```

Here, we use destructuring to extract the props into variables (`ingredients` and `onClick`) that we then use inside the component's function body.




---
---


<details>

<summary>  See More </summary>   







## Table of Contents

- [var versus let / const](#var-versus-let--const)
- [Replacing IIFEs with Blocks](#replacing-iifes-with-blocks)
- [Arrow Functions](#arrow-functions)
- [Strings](#strings)
- [Destructuring](#destructuring)
- [Modules](#modules)
- [Parameters](#parameters)
- [Classes](#classes)
- [Symbols](#symbols)
- [Maps](#maps)
- [WeakMaps](#weakmaps)
- [Promises](#promises)
- [Generators](#generators)
- [Async Await](#async-await)
- [Getter/Setter functions](#getter-and-setter-functions)
- [License](#license)

## var versus let / const

> Besides `var`, we now have access to two new identifiers for storing values
—`let` and `const`. Unlike `var`, `let` and `const` statements are not hoisted
to the top of their enclosing scope.

An example of using `var`:

```javascript
var snack = 'Meow Mix';

function getFood(food) {
    if (food) {
        var snack = 'Friskies';
        return snack;
    }
    return snack;
}

getFood(false); // undefined
```

However, observe what happens when we replace `var` using `let`:

```javascript
let snack = 'Meow Mix';

function getFood(food) {
    if (food) {
        let snack = 'Friskies';
        return snack;
    }
    return snack;
}

getFood(false); // 'Meow Mix'
```

This change in behavior highlights that we need to be careful when refactoring
legacy code which uses `var`. Blindly replacing instances of `var` with `let`
may lead to unexpected behavior.

> **Note**: `let` and `const` are block scoped. Therefore, referencing
block-scoped identifiers before they are defined will produce
a `ReferenceError`.

```javascript
console.log(x); // ReferenceError: x is not defined

let x = 'hi';
```

> **Best Practice**: Leave `var` declarations inside of legacy code to denote
that it needs to be carefully refactored. When working on a new codebase, use
`let` for variables that will change their value over time, and `const` for
variables which cannot be reassigned.

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Replacing IIFEs with Blocks

> A common use of **Immediately Invoked Function Expressions** is to enclose
values within its scope. In ES6, we now have the ability to create block-based
scopes and therefore are not limited purely to function-based scope.

```javascript
(function () {
    var food = 'Meow Mix';
}());

console.log(food); // Reference Error
```

Using ES6 Blocks:

```javascript
{
    let food = 'Meow Mix';
};

console.log(food); // Reference Error
```

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Arrow Functions

Often times we have nested functions in which we would like to preserve the
context of `this` from its lexical scope. An example is shown below:

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map(function (character) {
        return this.name + character; // Cannot read property 'name' of undefined
    });
};
```

One common solution to this problem is to store the context of `this` using
a variable:

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    var that = this; // Store the context of this
    return arr.map(function (character) {
        return that.name + character;
    });
};
```

We can also pass in the proper context of `this`:

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map(function (character) {
        return this.name + character;
    }, this);
};
```

As well as bind the context:

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map(function (character) {
        return this.name + character;
    }.bind(this));
};
```

Using **Arrow Functions**, the lexical value of `this` isn't shadowed and we
can re-write the above as shown:

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map(character => this.name + character);
};
```

> **Best Practice**: Use **Arrow Functions** whenever you need to preserve the
lexical value of `this`.

Arrow Functions are also more concise when used in function expressions which
simply return a value:

```javascript
var squares = arr.map(function (x) { return x * x }); // Function Expression
```

```javascript
const arr = [1, 2, 3, 4, 5];
const squares = arr.map(x => x * x); // Arrow Function for terser implementation
```

> **Best Practice**: Use **Arrow Functions** in place of function expressions
when possible.

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Strings

With ES6, the standard library has grown immensely. Along with these changes
are new methods which can be used on strings, such as `.includes()` and
`.repeat()`.

### .includes( )

```javascript
var string = 'food';
var substring = 'foo';

console.log(string.indexOf(substring) > -1);
```

Instead of checking for a return value `> -1` to denote string containment,
we can simply use `.includes()` which will return a boolean:

```javascript
const string = 'food';
const substring = 'foo';

console.log(string.includes(substring)); // true
```

### .repeat( )

```javascript
function repeat(string, count) {
    var strings = [];
    while(strings.length < count) {
        strings.push(string);
    }
    return strings.join('');
}
```

In ES6, we now have access to a terser implementation:

```javascript
// String.repeat(numberOfRepetitions)
'meow'.repeat(3); // 'meowmeowmeow'
```

### Template Literals

Using **Template Literals**, we can now construct strings that have special
characters in them without needing to escape them explicitly.

```javascript
var text = "This string contains \"double quotes\" which are escaped.";
```

```javascript
let text = `This string contains "double quotes" which don't need to be escaped anymore.`;
```

**Template Literals** also support interpolation, which makes the task of
concatenating strings and values:

```javascript
var name = 'Tiger';
var age = 13;

console.log('My cat is named ' + name + ' and is ' + age + ' years old.');
```

Much simpler:

```javascript
const name = 'Tiger';
const age = 13;

console.log(`My cat is named ${name} and is ${age} years old.`);
```

In ES5, we handled new lines as follows:

```javascript
var text = (
    'cat\n' +
    'dog\n' +
    'nickelodeon'
);
```

Or:

```javascript
var text = [
    'cat',
    'dog',
    'nickelodeon'
].join('\n');
```

**Template Literals** will preserve new lines for us without having to
explicitly place them in:

```javascript
let text = ( `cat
dog
nickelodeon`
);
```

**Template Literals** can accept expressions, as well:

```javascript
let today = new Date();
let text = `The time and date is ${today.toLocaleString()}`;
```

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Destructuring

Destructuring allows us to extract values from arrays and objects (even deeply
nested) and store them in variables with a more convenient syntax.

### Destructuring Arrays

```javascript
var arr = [1, 2, 3, 4];
var a = arr[0];
var b = arr[1];
var c = arr[2];
var d = arr[3];
```

```javascript
let [a, b, c, d] = [1, 2, 3, 4];

console.log(a); // 1
console.log(b); // 2
```

### Destructuring Objects

```javascript
var luke = { occupation: 'jedi', father: 'anakin' };
var occupation = luke.occupation; // 'jedi'
var father = luke.father; // 'anakin'
```

```javascript
let luke = { occupation: 'jedi', father: 'anakin' };
let {occupation, father} = luke;

console.log(occupation); // 'jedi'
console.log(father); // 'anakin'
```

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Modules

Prior to ES6, we used libraries such as [Browserify](http://browserify.org/)
to create modules on the client-side, and [require](https://nodejs.org/api/modules.html#modules_module_require_id)
in **Node.js**. With ES6, we can now directly use modules of all types
(AMD and CommonJS).

### Exporting in CommonJS

```javascript
module.exports = 1;
module.exports = { foo: 'bar' };
module.exports = ['foo', 'bar'];
module.exports = function bar () {};
```

### Exporting in ES6

With ES6, we have various flavors of exporting. We can perform
**Named Exports**:

```javascript
export let name = 'David';
export let age  = 25;​​
```

As well as **exporting a list** of objects:

```javascript
function sumTwo(a, b) {
    return a + b;
}

function sumThree(a, b, c) {
    return a + b + c;
}

export { sumTwo, sumThree };
```

We can also export functions, objects and values (etc.) simply by using the `export` keyword:

```javascript
export function sumTwo(a, b) {
    return a + b;
}

export function sumThree(a, b, c) {
    return a + b + c;
}
```

And lastly, we can **export default bindings**:

```javascript
function sumTwo(a, b) {
    return a + b;
}

function sumThree(a, b, c) {
    return a + b + c;
}

let api = {
    sumTwo,
    sumThree
};

export default api;

/* Which is the same as
 * export { api as default };
 */
```

> **Best Practices**: Always use the `export default` method at **the end** of
the module. It makes it clear what is being exported, and saves time by having
to figure out what name a value was exported as. More so, the common practice
in CommonJS modules is to export a single value or object. By sticking to this
paradigm, we make our code easily readable and allow ourselves to interpolate
between CommonJS and ES6 modules.

### Importing in ES6

ES6 provides us with various flavors of importing. We can import an entire file:

```javascript
import 'underscore';
```

> It is important to note that simply **importing an entire file will execute
all code at the top level of that file**.

Similar to Python, we have named imports:

```javascript
import { sumTwo, sumThree } from 'math/addition';
```

We can also rename the named imports:

```javascript
import {
    sumTwo as addTwoNumbers,
    sumThree as sumThreeNumbers
} from 'math/addition';
```

In addition, we can **import all the things** (also called namespace import):

```javascript
import * as util from 'math/addition';
```

Lastly, we can import a list of values from a module:

```javascript
import * as additionUtil from 'math/addition';
const { sumTwo, sumThree } = additionUtil;
```
Importing from the default binding like this:

```javascript
import api from 'math/addition';
// Same as: import { default as api } from 'math/addition';
```

While it is better to keep the exports simple, but we can sometimes mix default import and mixed import if needed.
When we are exporting like this:

```javascript
// foos.js
export { foo as default, foo1, foo2 };
```

We can import them like the following:

```javascript
import foo, { foo1, foo2 } from 'foos';
```

When importing a module exported using commonjs syntax (such as React) we can do:

```javascript
import React from 'react';
const { Component, PropTypes } = React;
```

This can also be simplified further, using:

```javascript
import React, { Component, PropTypes } from 'react';
```

> **Note**: Values that are exported are **bindings**, not references.
Therefore, changing the binding of a variable in one module will affect the
value within the exported module. Avoid changing the public interface of these
exported values.

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Parameters

In ES5, we had varying ways to handle functions which needed **default values**,
**indefinite arguments**, and **named parameters**. With ES6, we can accomplish
all of this and more using more concise syntax.

### Default Parameters

```javascript
function addTwoNumbers(x, y) {
    x = x || 0;
    y = y || 0;
    return x + y;
}
```

In ES6, we can simply supply default values for parameters in a function:

```javascript
function addTwoNumbers(x=0, y=0) {
    return x + y;
}
```

```javascript
addTwoNumbers(2, 4); // 6
addTwoNumbers(2); // 2
addTwoNumbers(); // 0
```

### Rest Parameters

In ES5, we handled an indefinite number of arguments like so:

```javascript
function logArguments() {
    for (var i=0; i < arguments.length; i++) {
        console.log(arguments[i]);
    }
}
```

Using the **rest** operator, we can pass in an indefinite amount of arguments:

```javascript
function logArguments(...args) {
    for (let arg of args) {
        console.log(arg);
    }
}
```

### Named Parameters

One of the patterns in ES5 to handle named parameters was to use the **options
object** pattern, adopted from jQuery.

```javascript
function initializeCanvas(options) {
    var height = options.height || 600;
    var width  = options.width  || 400;
    var lineStroke = options.lineStroke || 'black';
}
```

We can achieve the same functionality using destructuring as a formal parameter
to a function:

```javascript
function initializeCanvas(
    { height=600, width=400, lineStroke='black'}) {
        // Use variables height, width, lineStroke here
    }
```

If we want to make the entire value optional, we can do so by destructuring an
empty object:

```javascript
function initializeCanvas(
    { height=600, width=400, lineStroke='black'} = {}) {
        // ...
    }
```

### Spread Operator

In ES5, we could find the max of values in an array by using the `apply` method on `Math.max` like this:
```javascript
Math.max.apply(null, [-1, 100, 9001, -32]); // 9001
```

In ES6, we can now use the spread operator to pass an array of values to be used as
parameters to a function:

```javascript
Math.max(...[-1, 100, 9001, -32]); // 9001
```

We can concat array literals easily with this intuitive syntax:

```javascript
let cities = ['San Francisco', 'Los Angeles'];
let places = ['Miami', ...cities, 'Chicago']; // ['Miami', 'San Francisco', 'Los Angeles', 'Chicago']
```

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Classes

Prior to ES6, we implemented Classes by creating a constructor function and
adding properties by extending the prototype:

```javascript
function Person(name, age, gender) {
    this.name   = name;
    this.age    = age;
    this.gender = gender;
}

Person.prototype.incrementAge = function () {
    return this.age += 1;
};
```

And created extended classes by the following:

```javascript
function Personal(name, age, gender, occupation, hobby) {
    Person.call(this, name, age, gender);
    this.occupation = occupation;
    this.hobby = hobby;
}

Personal.prototype = Object.create(Person.prototype);
Personal.prototype.constructor = Personal;
Personal.prototype.incrementAge = function () {
    Person.prototype.incrementAge.call(this);
    this.age += 20;
    console.log(this.age);
};
```

ES6 provides much needed syntactic sugar for doing this under the hood. We can
create Classes directly:

```javascript
class Person {
    constructor(name, age, gender) {
        this.name   = name;
        this.age    = age;
        this.gender = gender;
    }

    incrementAge() {
      this.age += 1;
    }
}
```

And extend them using the `extends` keyword:

```javascript
class Personal extends Person {
    constructor(name, age, gender, occupation, hobby) {
        super(name, age, gender);
        this.occupation = occupation;
        this.hobby = hobby;
    }

    incrementAge() {
        super.incrementAge();
        this.age += 20;
        console.log(this.age);
    }
}
```

> **Best Practice**: While the syntax for creating classes in ES6 obscures how
implementation and prototypes work under the hood, it is a good feature for
beginners and allows us to write cleaner code.

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Symbols

Symbols have existed prior to ES6, but now we have a public interface to using
them directly. Symbols are immutable and unique and can be used as keys in any hash.

### Symbol( )

Calling `Symbol()` or `Symbol(description)` will create a unique symbol that cannot be looked up
globally. A Use case for `Symbol()` is to patch objects or namespaces from third parties with your own
logic, but be confident that you won't collide with updates to that library. For example,
if you wanted to add a method `refreshComponent` to the `React.Component` class, and be certain that
you didn't trample a method they add in a later update:

```javascript
const refreshComponent = Symbol();

React.Component.prototype[refreshComponent] = () => {
    // do something
}
```


### Symbol.for(key)

`Symbol.for(key)` will create a Symbol that is still immutable and unique, but can be looked up globally.
Two identical calls to `Symbol.for(key)` will return the same Symbol instance. NOTE: This is not true for
`Symbol(description)`:

```javascript
Symbol('foo') === Symbol('foo') // false
Symbol.for('foo') === Symbol('foo') // false
Symbol.for('foo') === Symbol.for('foo') // true
```

A common use case for Symbols, and in particular with `Symbol.for(key)` is for interoperability. This can be
achieved by having your code look for a Symbol member on object arguments from third parties that contain some
known interface. For example:

```javascript
function reader(obj) {
    const specialRead = Symbol.for('specialRead');
    if (obj[specialRead]) {
        const reader = obj[specialRead]();
        // do something with reader
    } else {
        throw new TypeError('object cannot be read');
    }
}
```

And then in another library:

```javascript
const specialRead = Symbol.for('specialRead');

class SomeReadableType {
    [specialRead]() {
        const reader = createSomeReaderFrom(this);
        return reader;
    }
}
```

> A notable example of Symbol use for interoperability is `Symbol.iterator` which exists on all iterable
types in ES6: Arrays, strings, generators, etc. When called as a method it returns an object with an Iterator
interface.

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Maps

**Maps** is a much needed data structure in JavaScript. Prior to ES6, we created
**hash** maps through objects:

```javascript
var map = new Object();
map[key1] = 'value1';
map[key2] = 'value2';
```

However, this does not protect us from accidentally overriding functions with
specific property names:

```javascript
> getOwnProperty({ hasOwnProperty: 'Hah, overwritten'}, 'Pwned');
> TypeError: Property 'hasOwnProperty' is not a function
```

Actual **Maps** allow us to `set`, `get` and `search` for values (and much more).

```javascript
let map = new Map();
> map.set('name', 'david');
> map.get('name'); // david
> map.has('name'); // true
```

The most amazing part of Maps is that we are no longer limited to just using
strings. We can now use any type as a key, and it will not be type-cast to
a string.

```javascript
let map = new Map([
    ['name', 'david'],
    [true, 'false'],
    [1, 'one'],
    [{}, 'object'],
    [function () {}, 'function']
]);

for (let key of map.keys()) {
    console.log(typeof key);
    // > string, boolean, number, object, function
}
```

> **Note**: Using non-primitive values such as functions or objects won't work
when testing equality using methods such as `map.get()`. As such, stick to
primitive values such as Strings, Booleans and Numbers.

We can also iterate over maps using `.entries()`:

```javascript
for (let [key, value] of map.entries()) {
    console.log(key, value);
}
```

<sup>[(back to table of contents)](#table-of-contents)</sup>

## WeakMaps

In order to store private data versions < ES6, we had various ways of doing this.
One such method was using naming conventions:

```javascript
class Person {
    constructor(age) {
        this._age = age;
    }

    _incrementAge() {
        this._age += 1;
    }
}
```

But naming conventions can cause confusion in a codebase and are not always
going to be upheld. Instead, we can use WeakMaps to store our values:

```javascript
let _age = new WeakMap();
class Person {
    constructor(age) {
        _age.set(this, age);
    }

    incrementAge() {
        let age = _age.get(this) + 1;
        _age.set(this, age);
        if (age > 50) {
            console.log('Midlife crisis');
        }
    }
}
```

The cool thing about using WeakMaps to store our private data is that their
keys do not give away the property names, which can be seen by using
`Reflect.ownKeys()`:

```javascript
> const person = new Person(50);
> person.incrementAge(); // 'Midlife crisis'
> Reflect.ownKeys(person); // []
```

A more practical example of using WeakMaps is to store data which is associated
to a DOM element without having to pollute the DOM itself:

```javascript
let map = new WeakMap();
let el  = document.getElementById('someElement');

// Store a weak reference to the element with a key
map.set(el, 'reference');

// Access the value of the element
let value = map.get(el); // 'reference'

// Remove the reference
el.parentNode.removeChild(el);
el = null;

// map is empty, since the element is destroyed
```

As shown above, once the object is destroyed by the garbage collector,
the WeakMap will automatically remove the key-value pair which was identified
by that object.

> **Note**: To further illustrate the usefulness of this example, consider how
jQuery stores a cache of objects corresponding to DOM elements which have
references. Using WeakMaps, jQuery can automatically free up any memory that
was associated with a particular DOM element once it has been removed from the
document. In general, WeakMaps are very useful for any library that wraps DOM
elements.

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Promises

Promises allow us to turn our horizontal code (callback hell):

```javascript
func1(function (value1) {
    func2(value1, function (value2) {
        func3(value2, function (value3) {
            func4(value3, function (value4) {
                func5(value4, function (value5) {
                    // Do something with value 5
                });
            });
        });
    });
});
```

Into vertical code:

```javascript
func1(value1)
    .then(func2)
    .then(func3)
    .then(func4)
    .then(func5, value5 => {
        // Do something with value 5
    });
```

Prior to ES6, we used [bluebird](https://github.com/petkaantonov/bluebird) or
[Q](https://github.com/kriskowal/q). Now we have Promises natively:

```javascript
new Promise((resolve, reject) =>
    reject(new Error('Failed to fulfill Promise')))
        .catch(reason => console.log(reason));
```

Where we have two handlers, **resolve** (a function called when the Promise is
**fulfilled**) and **reject** (a function called when the Promise is **rejected**).

> **Benefits of Promises**: Error Handling using a bunch of nested callbacks
can get chaotic. Using Promises, we have a clear path to bubbling errors up
and handling them appropriately. Moreover, the value of a Promise after it has
been resolved/rejected is immutable - it will never change.

Here is a practical example of using Promises:

```javascript
var request = require('request');

return new Promise((resolve, reject) => {
  request.get(url, (error, response, body) => {
    if (body) {
        resolve(JSON.parse(body));
      } else {
        resolve({});
      }
  });
});
```

We can also **parallelize** Promises to handle an array of asynchronous
operations by using `Promise.all()`:

```javascript
let urls = [
  '/api/commits',
  '/api/issues/opened',
  '/api/issues/assigned',
  '/api/issues/completed',
  '/api/issues/comments',
  '/api/pullrequests'
];

let promises = urls.map((url) => {
  return new Promise((resolve, reject) => {
    $.ajax({ url: url })
      .done((data) => {
        resolve(data);
      });
  });
});

Promise.all(promises)
  .then((results) => {
    // Do something with results of all our promises
 });
```

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Generators

Similar to how [Promises](https://github.com/DrkSephy/es6-cheatsheet#promises) allow us to avoid
[callback hell](http://callbackhell.com/), Generators allow us to flatten our code - giving our
asynchronous code a synchronous feel. Generators are essentially functions which we can
[pause their execution](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/yield)
and subsequently return the value of an expression.

A simple example of using generators is shown below:

```javascript
function* sillyGenerator() {
    yield 1;
    yield 2;
    yield 3;
    yield 4;
}

var generator = sillyGenerator();
> console.log(generator.next()); // { value: 1, done: false }
> console.log(generator.next()); // { value: 2, done: false }
> console.log(generator.next()); // { value: 3, done: false }
> console.log(generator.next()); // { value: 4, done: false }
```

Where [next](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator/next)
will allow us to push our generator forward and evaluate a new expression. While the above example is extremely
contrived, we can utilize Generators to write asynchronous code in a synchronous manner:

```javascript
// Hiding asynchronousity with Generators

function request(url) {
    getJSON(url, function(response) {
        generator.next(response);
    });
}
```

And here we write a generator function that will return our data:

```javascript
function* getData() {
    var entry1 = yield request('http://some_api/item1');
    var data1  = JSON.parse(entry1);
    var entry2 = yield request('http://some_api/item2');
    var data2  = JSON.parse(entry2);
}
```

By the power of `yield`, we are guaranteed that `entry1` will have the data needed to be parsed and stored
in `data1`.

While generators allow us to write asynchronous code in a synchronous manner, there is no clear
and easy path for error propagation. As such, as we can augment our generator with Promises:

```javascript
function request(url) {
    return new Promise((resolve, reject) => {
        getJSON(url, resolve);
    });
}
```

And we write a function which will step through our generator using `next` which in turn will utilize our
`request` method above to yield a Promise:

```javascript
function iterateGenerator(gen) {
    var generator = gen();
    (function iterate(val) {
        var ret = generator.next();
        if(!ret.done) {
            ret.value.then(iterate);
        }
    })();
}
```

By augmenting our Generator with Promises, we have a clear way of propagating errors through the use of our
Promise `.catch` and `reject`. To use our newly augmented Generator, it is as simple as before:

```javascript
iterateGenerator(function* getData() {
    var entry1 = yield request('http://some_api/item1');
    var data1  = JSON.parse(entry1);
    var entry2 = yield request('http://some_api/item2');
    var data2  = JSON.parse(entry2);
});
```

We were able to reuse our implementation to use our Generator as before, which shows their power. While Generators
and Promises allow us to write asynchronous code in a synchronous manner while retaining the ability to propagate
errors in a nice way, we can actually begin to utilize a simpler construction that provides the same benefits:
[async-await](https://github.com/DrkSephy/es6-cheatsheet#async-await).

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Async Await

While this is actually an upcoming ES2016 feature, `async await` allows us to perform the same thing we accomplished
using Generators and Promises with less effort:

```javascript
var request = require('request');

function getJSON(url) {
  return new Promise(function(resolve, reject) {
    request(url, function(error, response, body) {
      resolve(body);
    });
  });
}

async function main() {
  var data = await getJSON();
  console.log(data); // NOT undefined!
}

main();
```

Under the hood, it performs similarly to Generators. I highly recommend using them over Generators + Promises. A great resource
for getting up and running with ES7 and Babel can be found [here](http://masnun.com/2015/11/11/using-es7-asyncawait-today-with-babel.html).

<sup>[(back to table of contents)](#table-of-contents)</sup>
## Getter and setter functions

ES6 has started supporting getter and setter functions within classes. Using the following example:

```javascript
class Employee {

    constructor(name) {
        this._name = name;
    }

    get name() {
      if(this._name) {
        return 'Mr. ' + this._name.toUpperCase();  
      } else {
        return undefined;
      }  
    }

    set name(newName) {
      if (newName == this._name) {
        console.log('I already have this name.');
      } else if (newName) {
        this._name = newName;
      } else {
        return false;
      }
    }
}

var emp = new Employee("James Bond");

// uses the get method in the background
if (emp.name) {
  console.log(emp.name);  // Mr. JAMES BOND
}

// uses the setter in the background
emp.name = "Bond 007";
console.log(emp.name);  // Mr. BOND 007  
```

Latest browsers are also supporting getter/setter functions in Objects and we can use them for computed properties, adding listeners and preprocessing before setting/getting:

```javascript
var person = {
  firstName: 'James',
  lastName: 'Bond',
  get fullName() {
      console.log('Getting FullName');
      return this.firstName + ' ' + this.lastName;
  },
  set fullName (name) {
      console.log('Setting FullName');
      var words = name.toString().split(' ');
      this.firstName = words[0] || '';
      this.lastName = words[1] || '';
  }
}

person.fullName; // James Bond
person.fullName = 'Bond 007';
person.fullName; // Bond 007
```
<sup>[(back to table of contents)](#table-of-contents)</sup>





</details>

