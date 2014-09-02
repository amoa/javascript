# Addictive Mobility JavaScript Handbook
A Best Practices & Style Guide


* * *


## Table of Contents

1. [Introduction](#introduction)
2. [Guiding Principles](#guiding-principles)
3. [Basics](#basics)
  1. [Types](#types)
  2. [Strings](#strings)
  3. [Arrays](#arrays)
  4. [Objects](#objects)
  5. [Functions](#functions)
  6. [Variables](#variables)
  7. [Comments](#comments)
  8. [Strict Mode](#strict-mode)
4. [Style](#style)
  1. [Naming](#naming)
  2. [Indentation](#indentation)
  3. [Lines](#lines)
  4. [Whitespace](#whitespace)
  5. [Code Alignment](#code-alignment)
  6. [Punctuation](#punctuation)
  7. [Blocks](#blocks)
  8. [Documentation](#documentation)
5. [Idioms](#idioms)
  1. [Comparison](#comparison)
  2. [Property Checking](#property-checking)
  3. [Type Checking](#type-checking)
  4. [Default Arguments](#default-arguments)
  5. [Type Conversion](#type-conversion)
  6. [Context](#context)
  7. [Event Handling](#event-handling)
6. [Structure](#structure)
  1. [Modules](#modules)
  2. [Dependencies](#dependencies)
  3. [Filenames](#filenames)
  4. [Versioning](#versioning)
7. [Tools](#tools)
  1. [Sublime Text](#sublime-text)
  2. [JSHint](#jshint)
  3. [JSCS](#jscs)
8. [Notes](#notes)
  1. [ECMAScript / Browser Support](#ecmascript--browser-support)


* * *


## Introduction

In contrast to Python's "batteries included" and "one obvious way to do it" philosophy, JavaScript has a very small core library which encourages developers to expand creatively upon.
It can be very satisfying to code in JavaScript, as it is extremely versatile and supports multiple styles and paradigms of programming.

For better or for worse, Javascript's minimalist philosophy means that every developer ends up contributing their own opinion in their code.
Finding "the right way" to do things in JavaScript can sometimes be difficult.
In teams, this can lead to confusion when reading others' code or even conflict over what is considered "good" JavaScript.

This guide aims to outlines the code conventions used internally for JavaScript projects at Addictive Mobility.
Certain conventions exist because they are best practice; they should be followed by every JavaScript developer.
Others are simply opinionated (within reason) stylistic choices that exist simply to serve as common ground for developers.

[[top](#table-of-contents)]


* * *


## Guiding Principles

The intention of this guide is to help developers, as a team, effectively and efficiently produce error-free JavaScript code.
Where this guide differs from existing project conventions, __*follow existing project conventions*__.
Where this guide is inconsistent or lacking, let these be your guiding principles:

__Ease of Understanding__

Code is written once, but read many times; so write code that others (and _yourself_, at a later time) can understand.
Break up long lines of code into easily consumable chunks.
If you are doing anything clever or tricky, comment on it.
Provide the next developer who comes along with hints about _why_ you made the choices you did.

__Ease of Navigating__

Name and organize things in a common matter.
All developers on your team should feel as comfortable and as at home with your code as their own.

__Ease of Reviewing__

Short lines make code changes more obvious and diffs more useful.
Do not make too many changes or unrelated changes in the same commit.

__Ease of Maintaining__

With respect to time and budget constraints, write code that is reusable, testable, and built to last.
Constantly refactor and improve your code based on what you have learnt.

[[top](#table-of-contents)]


* * *


## Basics

### Types

Primitives:
- `Boolean`, `Number`, `String`, `null`, `undefined`
- Assigned by value

Complex
- `Array`, `Object`, `Function`
- Assigned by reference

[[top](#table-of-contents)]

- - -

### Strings

Always use literal syntax with single quotes `''`.
This is helpful when creating HTML strings whose attributes should be wrapped with double quotes.
```javascript
var foo = 'bar',
    html = '<div id="foo" class="bar">';
```

Always use string concatenation over multiline string literals:
```javascript
// Bad
var foobar = 'Lorem ipsum dolor sit amet, \
consectetur adipiscing elit. Etiam euismod \
lacus ac sapien varius, nec venenatis mi \
imperdiet.';

// Good
var foobar = 'Lorem ipsum dolor sit amet, ' +
    'consectetur adipiscing elit. Etiam euismod ' +
    'lacus ac sapien varius, nec venenatis mi ' +
    'imperdiet.';
```

When programmatically building up a string, use Array#join.

[[top](#table-of-contents)]

- - -

### Arrays

Use Array#push when adding items.
```javascript
var foo = [];

// bad
foo[foo.length] = 'bar';

// good
foo.push('bar');
```

To copy an array, use Array#slice
```javascript
var foo = [true, true, true, true],
    bar = foo.slice();
foo[0] = false;
console.log(bar[0]); // true
```

[[top](#table-of-contents)]

- - -

### Objects

Property names must be strings.
Prefer dot notation over bracket notation when the property name is a valid identifier.
Use bracket notation only when the property name is not a valid identifier, or when the object is accessed programmatically.
```javascript
var object = {};

// bad
object['foo'] = 'bar';

// good
object.foo = 'bar';

// acceptable
object['foo bar'] = 'foo bar';
```

Reserved keywords as property names are okay in ES5 and IE>=9. If they must be avoided, use readable synonyms instead.
```javascript
// okay in ES5 and IE>8
var superman = {
    class: 'alien'
};

// bad
var superman = {
    klass: 'alien'
};

// good
var superman = {
    type: 'alien'
};
```

Avoid adding properties or removing properties after initialization.
Reassigning properties is much faster on modern engines due to [hidden classes](http://www.html5rocks.com/en/tutorials/speed/v8/#toc-topic-hiddenclasses).
```javascript
// bad
delete superman.type;

// good
superman.type = null;

// bad
function Superhero() {}
Superhero.prototype.losePower = function () {
    delete this.power;
}
Superhero.prototype.addPower = function (power) {
    this.power = power;
}

// good
function Superhero() {
    this.power = null;
}
Superhero.prototype.losePower = function () {
    this.power = null;
}
Superhero.prototype.addPower = function (power) {
    this.power = power;
}
```

[[top](#table-of-contents)]

- - -

### Functions

Never declare functions in non-function blocks (`if`, `while`, `for`, etc).

Function statements and function exressions are both okay, depending on context.
```javascript
// function statement
function helloWorld() {}

// named function expression, okay in IE>=9
var helloWorld = function helloWorld() {};

// anonymous function expression
var helloWorld = function () {};
```

Named functions expressions [are okay in IE>=9](http://kangax.github.io/nfe/), and are preferred over anonymous function expressions for better stack traces.
```javascript
// preferred in IE>=9
this.on('click', function onClick() {
    ...
})

// not preferred
this.on('click', function () {
    ...
})
```

Returning early is encouraged and promotes readability and error catching.
```javascript
function addNumbers(a, b) {
    if (typeof a !== 'number' || typeof b !== 'number') {
        return null;
    }
    return a + b;
}
```

[[top](#table-of-contents)]

- - -

### Variables

Always declare your variables using `var`:
```javascript
// bad
a = 1;

// good
var a = 1;
```

[[top](#table-of-contents)]

- - -

### Comments

Comments should always focus on the _why_ and not the _how_.

Always place comments on its own line, preceding the subject of the comment.
This makes it easier to differentiate comment changes from code changes during review.
Never place comments at the end of a line.

Comments should be preceded by an empty line or another comment. Leave an empty line after the subject of the comment.
```javascript
// bad
user.getAge() // Get a user's age

// good
// Get a user's age
user.getAge();

/**
 * Multi-line comments should be used only as headers
 * for functions, files, or sections of code
 */
function doSomething() {}
```

[[top](#table-of-contents)]

- - -

### Strict Mode

Always enable strict mode; it will help you catch bugs.
```javascript
'use strict';
```

[[top](#table-of-contents)]


* * *


## Style

### Naming

A valid JavaScript identifier is a sequence of alphanumeric characters, including underscores (_) and dollar signs ($), that does not begin with a number.

Naming should follow the following conventions:
- Variable names should be descriptive, and as long as necessary:
```javascript
// bad
var mp, udomain;

// good
var mobilePhone, urlDomain;
```
- Use camelCasing for names with multiple words:
```javascript
var captainAmerica, unitedStatesOfAmerica;
```
- Use plural form for arrays used as lists and objects used as hashmaps:
```javascript
var sports = ['baseball', 'basketball', 'hockey'],
    players = {
        'Babe Ruth': 'baseball',
        'Michael Jordan': 'basketball',
        'Wayne Gretsky': 'hockey'
    };
```
- Acronyms should be treated like regular words, to allow consecutive acronyms:
```javascript
var xmlHttpRequest;
```
- Use PascalCase for constructors:
```javascript
var Student = function Student(){};
```
- Use UPPER_SNAKE_CASE when defining constant-like variables:
```javascript
var DO_NOT_MODIFY_ME;
```
- Use $dollarSign prefix to denote a jQuery object
```javascript
var $body = $('body');
console.log($body instanceof jQuery); // true
```
- Use _underscore prefix to denote 'private', temporary, or otherwise less accessible variables
```javascript
// Hiding internal 'do not touch' properties
var person = {
    _name: 'John Smith',
    name: function () { return this._name; }
};

// Making variables returned in callbacks accessible outside their scope
var document;
retrieveDocument(function (err, _document) {
    document = _document;
});
```
- Use `_this` to alias the current context.
'self' and 'that' are also common aliases for context, but 'that' semantically contradicts the meaning of 'this' and 'self' is a global variable in all browsers making it unsafe to use.
```javascript
// bad
function closure() {
    var self = this;
}
function closure() {
    var that = this;
}

// good
function closure() {
    var _this = this;
}
```

[[top](#table-of-contents)]

- - -

## Indentation

Always indent with 4 spaces; variable names line up nicely this way.
Never indent with tabs.
```javascript
var firstVariable,
    secondVariable,
    thirdVariable
```

[[top](#table-of-contents)]

- - -

### Lines

Lines should be 100 characters or less, preferably, and must have a hard cap at 120 characters.

[[top](#table-of-contents)]

- - -

### Whitespace

Always trim trailing whitespace.
Statements and expressions should be formatted as follows:
```javascript
var shortObject = {a: 1, b: 2},
	shortArray = [1, 2, 3];

var longObject = {
    secondLevelObject: {
        thirdLevelObject: {
        	fourthLevelArray: [
                'string 1',
                'string 2',
                'string 3'
            ],
            shortArray: [1, 2, 3]
        },
        shortObject: {a: 1, b: 2}
    }
};

switch (character) {
case 'Mickey':
    doSomething();
    break;
case 'Minnie':
    doSomething();
    break;
case 'Goofy':
    doSomething();
    break;
}

var result = (1 + 1) * (2 / 2);

function log(message, options) {
    console.log(message);
}

object.callMethod(argument1, argument2, function (err, results) {
    console.log(results);
});

(function () {
    console.log('IIFE');
}())
```

[[top](#table-of-contents)]

- - -

## Code Alignment

Avoid vertically aligning code.
Vertical alignment creates addditional work to maintain the visual aesthetic, and makes diffs less readable.
```javascript
// bad
var configs = {
    backgroundColor : '#DDDDDD',
    textColor       : '#444444',
    highlightColor  : '#FF0000',
    shadowColor     : '#AA0000'
};

// good
var configs = {
    backgroundColor: '#DDDDDD',
    textColor: '#444444',
    highlightColor: '#FF0000',
    shadowColor: '#AA0000'
};
```

[[top](#table-of-contents)]

- - -

## Punctuation

Always end lines with semicolons.
```javascript
// bad
var foo = 1

// good
var foo = 1;
```

When breaking long statements into multiple lines, always place punctuation on preceding line to avoid automatic semicolon insertion.
```javascript
// bad
var apples = 1
,   oranges = 2
,   bananas = 3;

if (apples
    && (oranges >= 2)
    && (bananas < 5)
) {
    ...
}

var fruits = apples
    ? bananas : oranges;

// good
var apples = 1,
    oranges = 2,
    bananas = 3;

if (apples &&
    (oranges >= 2) &&
    (bananas < 5)
) {
    ...
}

var fruits = apples ?
    bananas : oranges;
```

An exception is made for chained method calls
```javascript
$element.appendTo($body)
    .siblings()
    .hide();
```

[[top](#table-of-contents)]

- - -

### Blocks

Curly braces are always required. One-liners are okay, as long as they contain only one statement.
```javascript
// bad
if (true) doSomething();
if (true) { doSomething(); doSomethingElse(); }

// good
if (true) { doSomething(); }
if (true) {
	doSomething();
    doSomethingElse();
}
```

[[top](#table-of-contents)]

- - -

### Documentation

Document your code with [JSDoc](http://usejsdoc.org/).
```javascript
/**
 * Represents a book.
 * @constructor
 * @param {string} title - The title of the book.
 * @param {string} author - The author of the book.
 */
function Book(title, author) {
    ...
}
```

[[top](#table-of-contents)]


* * *


## Idioms

### Comparison

Always check for strict equality with `===`.
```javascript
// bad
if (count == 1) { doSomething(); }

// good
if (count === 1) { doSomething(); }
```

Never use `==`, except when comparing with `null`.
Loose comparison with null offers a convenient way to check for `null` and `undefined` at the same time.
```javascript
// allowed
function hasParameter(parameter) {
    if (parameter == null) {
        return false;
    }
    return true;
}
```

[[top](#table-of-contents)]

- - -

### Property Checking

Using the `in` operator is much compact, but using the `typeof` is
[much more performant](http://jsperf.com/in-operator-vs-typeof) in modern browsers.
Use `in` where readability matters, use `typeof` where performance matters.
```javascript
// Check if 'foo' contains property 'bar'

// ...using the 'in' operator
if ('bar' in foo) {
    doSomething();
}

// ...using the 'typeof' operator
if (typeof foo.bar !== 'undefined') {
    doSomething();
}
```

[[top](#table-of-contents)]

- - -

### Type Checking

If a utility library like Underscore or Lo-Dash is available, use the available methods.
Otherwise,

Use `typeof` for most primitives and functions:
```javascript
console.log(typeof true === 'boolean');
console.log(typeof 1 === 'number');
console.log(typeof 'foo' === 'string');
console.log(typeof undefined === 'undefined');
console.log(typeof function (){} === 'function');
```

Use direct comparison for `null`:
```javascript
console.log(null === null);
```

Use `instanceof` for complex objects:
```javascript
console.log([] instanceof Array);
console.log({} instanceof Object);
```

If you are concerned with multiple contexts, then use Object#toString:
```javascript
console.log(Object.prototype.toString.call([]) === '[object Array]');
console.log(Object.prototype.toString.call({}) === '[object Object]');
```

[[top](#table-of-contents)]

- - -

### Default Arguments

If falsy values like zeroes `0` and empty strings `''` can be ignored:
```javascript
function (optionalArgument) {
    optionalArgument = optionalArgument || defaultValue;
}

function (options) {
    options = options || {};
    var option1 = options.options1 || defaultValue;
    var option2 = options.options2 || defaultValue;
}
```
If you are expecting zeroes `0` or empty string `''`:
```javascript
function (options) {
    optionalArgument = (optionalArgument != null) ?
        optionalArgument : defaultValue;
}
```

[[top](#table-of-contents)]

- - -

### Type Conversion

__Strings__

Use the built-in #toString() or implicit type coercion.
When coercing, make the conversion visible by placing the coercion at the beginning of the statement.

```javascript
var number = 1000,
    boolean = true,
    date = new Date();

// bad
console.log(number + '');

// good
console.log(number.toString()); // '1000'
console.log('' + boolean); // 'true'
console.log('' + date); // 'Sun Aug 24 2014 21:19:29 GMT-0400 (EDT)'
```

__Numbers__

Never overcomplicate things by using bitwise operators.

Use `Number` when converting from Boolean or when the input string must contain only digits (exponents are an exception).
```javascript
console.log(Number(true)); // 1
console.log(Number('100')); // 100
console.log(Number('10e5')); // 1000000
console.log(Number('100px')); // NaN
```

Use `parseInt`, `parseFloat` when parsing strings up to the first non-digit character.
When using `parseInt`, always provide the optional `radix` argument.
```javascript
console.log(parseInt('100', 10)); // 100
console.log(parseInt('10e5', 10)); // NaN
console.log(parseInt('100px' 10)); // 100
```

__Booleans__

Convert any type to a boolean by applying `!!`.
Keep in mind:
- Arrays and objects are _always_ truthy, regardless or length or keys.
```javascript
console.log(!![]); // true
console.log(!!{}); // true
```
- Numbers are truthy when non-zero
```javascript
console.log(!!0); // false
console.log(!!1); // true
console.log(!!-1); // true
```
- Strings' truthiness depends on String#length, i.e. strings are truthy when non-empty
```javascript
console.log(!!''); // false
console.log(!!'foobar'); // true
console.log(!!'0'); // true
console.log(!!'false'); // true
```
- `null`, `undefined`, `NaN` are always falsy

[[top](#table-of-contents)]

- - -

### Context

When a function is called, its context is set to its caller, not its owner.
Newcomers to the language are often run into context problems, especially in asynchronous method callbacks.
```javascript
// error
function Person(firstName) {
    this.firstName = firstName;
    this.asyncGreet = function () {
        setTimeout(function () {
            console.log('Hi, my name is ' + this.firstName);
        }, 0);
    };
}
var peter = new Person('Peter');
peter.asyncGreet(); // 'Hi, my name is undefined'
```

The preferred method of context binding is to use Function#bind, available in ES5 and IE >=9. Utility libraries like Underscore or Lo-Dash typically also provide a shim.
```javascript
// preferred
function Person(firstName) {
    this.firstName = firstName;
    this.asyncGreet = function () {
        setTimeout(function () {
            console.log('Hi, my name is ' + this.firstName);
        }.bind(this), 0);
    };
}
var peter = new Person('Peter');
peter.asyncGreet(); // 'Hi, my name is Peter'
```

In the worst case scenario, fall back to aliasing.
Use `_this` to alias `this`.
```javascript
// preferred
function Person(firstName) {
    var _this = this;
    this.firstName = firstName;
    this.asyncGreet = function () {
        setTimeout(function () {
            console.log('Hi, my name is ' + _this.firstName);
        }.bind(this), 0);
    };
}
var peter = new Person('Peter');
peter.asyncGreet(); // 'Hi, my name is Peter'
```

[[top](#table-of-contents)]

- - -

### Event Handling

Event handlers should only handle events.
Factoring out the actual function allows it to be tested.
```javascript
// bad
function onClick(event) {
    var popup = document.getElementById("popup");
    popup.style.left = event.clientX + "px";
    popup.style.top = event.clientY + "px";
    popup.className = "show";
}

// good
function onClick(event){
    showPopup(event.clientX, event.clientY);
}

function showPopup(x, y){
    var popup = document.getElementById("popup");
    popup.style.left = x + "px";
    popup.style.top = y + "px";
    popup.className = "show";
}
```

[[top](#table-of-contents)]


* * *


## Structure

### Modules

CommonJS is the one true way.
It works natively on the server-side with Node.js _and_ on the client-side by compiling with Browserify.

[[top](#table-of-contents)]

- - -

### Dependencies

If you are using Node.js, then you should already be using [NPM](https://www.npmjs.org/) to manage your dependencies and have your dependencies saved in package.json.

Always use the `--save` flag to automatically add the dependency to package.json.
Always use the `--save-exact` flag to to prevent auto-updates.
While the vast majority of NPM packages follow semantic versioning, it [cannot be depended upon](https://github.com/jashkenas/underscore/issues/1805).

```
npm install --save --save-exact lo-dash
```

[[top](#table-of-contents)]

- - -

### Filenames

Filenames should be lowercase characters separated by dashes.
Plugins can prefix their dependency name, separated by a dot.
Versions can be denoted by a dash followed by the version number.
```
filename.js
filename-template.js
library.plugin.js
library-1.0.2.js
```

[[top](#table-of-contents)]

- - -

### Versioning

If your script provides a public API of any kind, use [Semantic Versioning](http://semver.org/).

[[top](#table-of-contents)]


* * *


## Tools

### Sublime Text

Recommended settings for Sublime Text.

Place in any of:
- *.sublime-project
- Preferences > Settings - User
- Preferences > Settings - More > Syntax Specific - User

```json
{
    "settings": {
        "rulers": [100, 120],
        "tab_size": 4,
        "translate_tabs_to_spaces": true,
        "trim_trailing_white_space_on_save": true
    }
}
```

Recommended packages:

- __[JSHint Gutter](https://sublime.wbond.net/packages/JSHint%20Gutter)__.
Catch bugs as you type with these settings:
```json
{
    "lint_on_load": true,
    "lint_on_save": true,
    "highlight_selected_regions": true,

    // Sublime Text 3 only.
    "lint_on_edit": true,
    "lint_on_edit_timeout": 0.3
}
```

[[top](#table-of-contents)]

- - -

### JSHint

A [static code analysis tool](https://github.com/jshint/jshint/) for JavaScript.
Add a pre-commit hook to catch common bugs before comitting.

[[top](#table-of-contents)]

- - -

### JSCS

A JavaScript [code style checker](https://github.com/mdevils/node-jscs).
Add a pre-commit hook to catch styling issues before committing.

[[top](#table-of-contents)]


* * *


## Notes

### ECMAScript / Browser Support

Write JavaScript to target ECMAScript 5 environments.
Supported browsers include Internet Explorer 9+ and current and previous major releases of Chrome, Safari, Firefox. 
Experimental ECMAScript 6 features should only be used on the server side, with precaution.


* * *
