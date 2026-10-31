# Unit 11 --- JavaScript and the DOM

## Engineer Training & Quick Reference

**Audience:** Web development engineers and trainees\
**Purpose:** Learn the JavaScript fundamentals covered in Unit 11,
practise them with runnable examples, and keep this document as a
reference during development.\
**Requirements:** A modern web browser and a text editor such as Visual
Studio Code.

------------------------------------------------------------------------

## Table of Contents

1.  [JavaScript Overview](#1-javascript-overview)
2.  [Embedding JavaScript](#2-embedding-javascript)
3.  [Syntax, Variables, Data Types, and
    Operators](#3-syntax-variables-data-types-and-operators)
4.  [Conditional Statements](#4-conditional-statements)
5.  [Loops, `break`, and `continue`](#5-loops-break-and-continue)
6.  [Functions](#6-functions)
7.  [Arrays and Array Methods](#7-arrays-and-array-methods)
8.  [Objects and Iteration](#8-objects-and-iteration)
9.  [The Document Object Model (DOM)](#9-the-document-object-model-dom)
10. [Selecting and Manipulating
    Elements](#10-selecting-and-manipulating-elements)
11. [Creating and Inserting Content](#11-creating-and-inserting-content)
12. [Browser Events](#12-browser-events)
13. [String Methods: `match()`, `split()`, `trim()`, and
    `join()`](#13-string-methods-match-split-trim-and-join)
14. [Modern JavaScript (ES6+)](#14-modern-javascript-es6)
15. [Classes and Modules](#15-classes-and-modules)
16. [Integrated Practice Project](#16-integrated-practice-project)
17. [Engineering Best Practices](#17-engineering-best-practices)

------------------------------------------------------------------------

## 1. JavaScript Overview

JavaScript is a programming language commonly used to make web pages
interactive.

-   **HTML** defines the structure and content of a page.
-   **CSS** controls the presentation and visual design.
-   **JavaScript** adds behaviour, logic, and dynamic updates.

JavaScript can run in the browser (client side). It can also run on a
server using a runtime such as Node.js (server side).

### Example: Respond to a button click

``` html
<button id="welcomeButton">Say hello</button>
<p id="message"></p>

<script>
  document.getElementById("welcomeButton").addEventListener("click", function () {
    document.getElementById("message").textContent = "Hello, engineer!";
  });
</script>
```

**Explanation**

1.  HTML creates the button and paragraph.
2.  `getElementById()` finds each element by its `id`.
3.  `addEventListener("click", ...)` registers code to run when the
    button is clicked.
4.  `textContent` updates the paragraph's text without reloading the
    page.

------------------------------------------------------------------------

## 2. Embedding JavaScript

JavaScript can be added to a page in three common ways.

### 2.1 External file --- recommended for most projects

**`index.html`**

``` html
<!doctype html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>JavaScript Demo</title>
  <script src="script.js" defer></script>
</head>
<body>
  <h1 id="heading">Original heading</h1>
</body>
</html>
```

**`script.js`**

``` javascript
const heading = document.getElementById("heading");
heading.textContent = "JavaScript is running!";
```

**Explanation:** The `src` attribute loads the external file. `defer`
tells the browser to execute the script after the HTML has been parsed,
so the target element is available. External files improve organisation,
readability, and reuse.

### 2.2 Internal `<script>` element

``` html
<script>
  const courseName = "JavaScript";
  console.log(courseName);
</script>
```

**Explanation:** The code is written directly in the HTML document. This
can be useful for small demonstrations, but large scripts are easier to
maintain in separate files.

### 2.3 Inline event attribute

``` html
<button onclick="alert('Welcome!')">Click me</button>
```

**Explanation:** The action is written in an HTML attribute. It works
for a tiny demonstration, but mixing JavaScript and HTML makes larger
applications harder to maintain. Prefer `addEventListener()`.

### Run a JavaScript example

1.  Create an `index.html` file.
2.  Create a `script.js` file if using an external script.
3.  Open the HTML file in a browser.
4.  Open Developer Tools and select **Console** to inspect
    `console.log()` output and errors.

------------------------------------------------------------------------

## 3. Syntax, Variables, Data Types, and Operators

### 3.1 Basic syntax

JavaScript is case-sensitive. Blocks use curly braces `{ }`, function
calls use parentheses `( )`, and statements are commonly separated by
line breaks or semicolons.

``` javascript
const firstName = "Mona";
const score = 92;

if (score >= 60) {
  console.log(`${firstName} passed.`);
}
```

**Explanation:** `score >= 60` is a condition. If it evaluates to
`true`, the code inside the `if` block runs. Consistent indentation
makes code easier to review.

### 3.2 Declaring variables: `const`, `let`, and `var`

``` javascript
const course = "Web Development"; // Reassignment is not allowed
let attempts = 1;                  // Reassignment is allowed
attempts = 2;

var legacyValue = "Older declaration style";
```

**Explanation**

-   Use `const` by default when the variable will not be reassigned.
-   Use `let` when reassignment is required.
-   `var` is the older declaration style and has function scope rather
    than block scope; avoid it in most new code.
-   A `const` object or array can still have its contents changed;
    `const` prevents reassignment of the variable binding.

### 3.3 Common data types

``` javascript
const learnerName = "Ali";             // String
const age = 18;                         // Number
const isRegistered = true;              // Boolean
const selectedValue = null;             // Intentional absence of a value
let notAssignedYet;                      // undefined
const skills = ["HTML", "CSS", "JS"];    // Array
const learner = { name: "Ali", age: 18 }; // Object

console.log(typeof learnerName); // "string"
console.log(typeof age);         // "number"
console.log(typeof isRegistered);// "boolean"
```

**Explanation:** Strings represent text, numbers represent numeric
values, booleans represent `true`/`false`, arrays hold ordered
collections, and objects store named properties. `null` is commonly used
to represent an intentionally empty value; an uninitialised variable has
the value `undefined`.

### 3.4 Operators

``` javascript
const a = 10;
const b = 3;

console.log(a + b);  // 13: addition
console.log(a - b);  // 7: subtraction
console.log(a * b);  // 30: multiplication
console.log(a / b);  // 3.333...: division
console.log(a % b);  // 1: remainder

console.log(a > b);       // true
console.log(a === 10);    // true: strict equality
console.log(a !== b);     // true: strict inequality
console.log(a > 5 && b < 5); // true: logical AND
console.log(a > 20 || b === 3); // true: logical OR
```

**Explanation:** Arithmetic operators calculate values; comparison
operators return booleans; logical operators combine conditions. Prefer
`===` and `!==` to avoid unexpected type conversion associated with `==`
and `!=`.

------------------------------------------------------------------------

## 4. Conditional Statements

Conditional statements choose which block of code runs.

### 4.1 `if` and `if...else`

``` javascript
const score = 76;

if (score >= 60) {
  console.log("Passed");
} else {
  console.log("Try again");
}
```

**Explanation:** The first block runs when the condition is true. The
`else` block runs otherwise.

### 4.2 `if...else if...else`

``` javascript
const score = 84;

if (score >= 90) {
  console.log("Excellent");
} else if (score >= 75) {
  console.log("Very good");
} else if (score >= 60) {
  console.log("Passed");
} else {
  console.log("Needs improvement");
}
```

**Explanation:** Conditions are tested from top to bottom. The first
matching branch runs, so order the conditions carefully.

### 4.3 `switch`

``` javascript
const role = "engineer";

switch (role) {
  case "admin":
    console.log("Full access");
    break;
  case "engineer":
    console.log("Development access");
    break;
  case "trainee":
    console.log("Learning access");
    break;
  default:
    console.log("Unknown role");
}
```

**Explanation:** `switch` is useful when one expression is compared
against several known values. `break` prevents execution from falling
through into the next case. `default` handles unmatched values.

### 4.4 Ternary operator

``` javascript
const age = 20;
const accessMessage = age >= 18 ? "Access granted" : "Access denied";

console.log(accessMessage);
```

**Explanation:** The condition before `?` selects the expression before
`:` when true and the expression after `:` when false. Use it for short
expressions, not deeply nested logic.

------------------------------------------------------------------------

## 5. Loops, `break`, and `continue`

Loops repeat a block of code.

### 5.1 `for` loop

``` javascript
for (let i = 1; i <= 5; i++) {
  console.log(`Iteration ${i}`);
}
```

**Explanation:** The initialisation (`let i = 1`) runs once; the
condition is checked before each iteration; `i++` updates the counter. A
`for` loop is useful when the number of iterations is known.

### 5.2 `while` loop

``` javascript
let countdown = 3;

while (countdown > 0) {
  console.log(countdown);
  countdown--;
}
```

**Explanation:** The condition is checked before each iteration. Make
sure the loop changes the state involved in its condition, or it may
never end.

### 5.3 `do...while` loop

``` javascript
let attempts = 0;

do {
  console.log("This runs at least once");
  attempts++;
} while (attempts < 1);
```

**Explanation:** The body executes before the condition is checked, so
it always runs at least once.

### 5.4 `break` and `continue`

``` javascript
for (let number = 1; number <= 6; number++) {
  if (number === 2) {
    continue; // Skip this iteration
  }

  if (number === 5) {
    break; // Stop the loop completely
  }

  console.log(number); // 1, 3, 4
}
```

**Explanation:** `continue` skips the rest of the current iteration and
proceeds to the next one. `break` exits the loop entirely.

------------------------------------------------------------------------

## 6. Functions

A function is a reusable block of code that performs a task. It can
accept input through parameters and return a result.

### 6.1 Function declaration

``` javascript
function multiply(a, b) {
  return a * b;
}

const result = multiply(4, 5);
console.log(result); // 20
```

**Explanation:** `a` and `b` are parameters; `4` and `5` are arguments.
`return` sends the calculated value back to the caller. A function
without an explicit return value returns `undefined`.

### 6.2 Function expression

``` javascript
const calculateArea = function (width, height) {
  return width * height;
};

console.log(calculateArea(4, 3)); // 12
```

**Explanation:** The function is assigned to a variable. Function
expressions are useful when passing a function as a value or controlling
where it is defined.

### 6.3 Arrow function

``` javascript
const subtract = (a, b) => a - b;

const greet = name => `Hello, ${name}`;

console.log(subtract(9, 4)); // 5
console.log(greet("Sara"));  // Hello, Sara
```

**Explanation:** Arrow functions provide concise syntax. A single
expression can be returned implicitly. Arrow functions use lexical
`this`, so they are not a direct replacement for every traditional
function, especially methods that need their own `this`.

### 6.4 Default parameters

``` javascript
function greetUser(name = "Guest") {
  return `Welcome, ${name}!`;
}

console.log(greetUser());       // Welcome, Guest!
console.log(greetUser("Omar")); // Welcome, Omar!
```

**Explanation:** A default parameter is used when the caller omits the
argument or passes `undefined`.

### 6.5 Useful built-in functions and methods

``` javascript
console.log(Math.abs(-8));          // 8
console.log(Math.ceil(4.2));        // 5
console.log(Math.floor(4.8));       // 4
console.log(Math.round(4.5));       // 5
console.log(Math.max(4, 9, 2));     // 9
console.log(Math.min(4, 9, 2));     // 2
console.log(Math.random());         // Number >= 0 and < 1

const text = "  JavaScript Engineer  ";
console.log(text.length);           // Length including spaces
console.log(text.toUpperCase());    // Uppercase copy
console.log(text.toLowerCase());    // Lowercase copy
console.log(text.indexOf("Script")); // Starting index, or -1
console.log(text.substring(2, 12)); // Portion of the string
console.log(text.trim());           // Remove surrounding whitespace
console.log("red,green,blue".split(",")); // ["red", "green", "blue"]
```

**Explanation:** `Math` provides numeric utilities. String methods
return results based on the original string; strings are immutable, so
these methods do not change the original string.

------------------------------------------------------------------------

## 7. Arrays and Array Methods

An array stores an ordered list of values. Array indexes start at `0`.

### 7.1 Create, read, and update an array

``` javascript
const skills = ["HTML", "CSS", "JavaScript"];

console.log(skills[0]); // HTML
console.log(skills[2]); // JavaScript

skills[1] = "CSS3";
console.log(skills); // ["HTML", "CSS3", "JavaScript"]
console.log(skills.length); // 3
```

**Explanation:** Use an index to access an item and `length` to find the
number of elements. Although `skills` is declared with `const`, its
contents can be updated; the variable itself cannot be reassigned to a
different array.

### 7.2 Common array methods

``` javascript
const numbers = [1, 2, 3, 4, 5];

console.log(numbers.join(" - "));       // "1 - 2 - 3 - 4 - 5"
console.log(numbers.slice(1, 4));       // [2, 3, 4]
console.log(numbers.concat([6, 7]));    // [1, 2, 3, 4, 5, 6, 7]
console.log(numbers.map(n => n * 2));   // [2, 4, 6, 8, 10]
console.log(numbers.filter(n => n > 3));// [4, 5]
console.log(numbers.reduce((sum, n) => sum + n, 0)); // 15
numbers.forEach(n => console.log(n));   // Logs each value
```

**Explanation**

-   `join()` creates a string from array elements.
-   `slice(start, end)` returns a portion without changing the original
    array; `end` is excluded.
-   `concat()` returns a combined array.
-   `map()` transforms each element and returns a new array.
-   `filter()` returns elements that pass a test.
-   `reduce()` combines elements into one accumulated result.
-   `forEach()` performs an action for each element and does not create
    a transformed array.

### 7.3 `splice()`, `reverse()`, and `sort()`

``` javascript
const names = ["Mona", "Ali", "Sara"];

names.splice(1, 1, "Omar");
console.log(names); // ["Mona", "Omar", "Sara"]

const reversed = [...names].reverse();
console.log(reversed); // ["Sara", "Omar", "Mona"]

const scores = [30, 4, 100, 21];
scores.sort((a, b) => a - b);
console.log(scores); // [4, 21, 30, 100]
```

**Explanation:** `splice()` changes the original array by
adding/removing elements. `reverse()` also changes the array it is
called on, so a copied array is used above. Default `sort()` compares
values as strings; provide a numeric comparator to sort numbers
correctly.

### 7.4 Choosing an iteration method

``` javascript
const trainees = ["Ali", "Mona", "Sara"];

for (let i = 0; i < trainees.length; i++) {
  console.log(trainees[i]);
}

for (const trainee of trainees) {
  console.log(trainee);
}

trainees.forEach(trainee => console.log(trainee));
```

**Explanation:** A classic `for` loop is useful when the index is
needed. `for...of` iterates over values in an iterable such as an array.
`forEach()` is useful for running a callback for each item. Avoid using
`for...in` to iterate through array values; it is primarily used for
enumerable object keys.

------------------------------------------------------------------------

## 8. Objects and Iteration

An object stores related data as key-value pairs.

``` javascript
const trainee = {
  name: "Mona",
  age: 19,
  track: "Web Development"
};

console.log(trainee.name);       // Mona
console.log(trainee["track"]);   // Web Development

trainee.age = 20;
trainee.email = "mona@example.com";

console.log(trainee);
```

**Explanation:** Dot notation is convenient when the property name is
known. Bracket notation is useful when the property name is stored in a
variable or contains characters that cannot be used in dot notation.

### Iterate through object properties

``` javascript
const trainee = {
  name: "Mona",
  track: "Web Development",
  active: true
};

for (const key in trainee) {
  console.log(`${key}: ${trainee[key]}`);
}

console.log(Object.keys(trainee));   // Array of keys
console.log(Object.values(trainee)); // Array of values
console.log(Object.entries(trainee));// Array of [key, value] pairs
```

**Explanation:** `for...in` iterates over enumerable property keys.
`Object.keys()`, `Object.values()`, and `Object.entries()` provide
arrays that can be processed with array methods. For objects with
inherited properties, use care with `for...in`; check ownership when
necessary.

------------------------------------------------------------------------

## 9. The Document Object Model (DOM)

The DOM is a programming interface that represents an HTML document as a
tree of nodes. JavaScript can use the DOM to read or change page
content, structure, attributes, and styles.

For example, in this HTML:

``` html
<main>
  <h1 id="page-title">Training</h1>
  <p class="description">Learn JavaScript.</p>
</main>
```

The `main` element contains the heading and paragraph as child elements.
JavaScript can select these nodes and update them.

``` javascript
const title = document.getElementById("page-title");
title.textContent = "JavaScript and the DOM";
```

**Explanation:** The browser creates the DOM from the HTML. `document`
is the entry point for working with the current page. Updating a DOM
node changes what the browser displays without needing to rebuild the
entire page.

------------------------------------------------------------------------

## 10. Selecting and Manipulating Elements

### 10.1 Selecting elements

Assume the page contains:

``` html
<h1 id="title">Welcome</h1>
<p class="note">First note</p>
<p class="note">Second note</p>
```

``` javascript
const title = document.getElementById("title");
const firstNote = document.querySelector(".note");
const allNotes = document.querySelectorAll(".note");
const paragraphs = document.getElementsByTagName("p");
const notesByClass = document.getElementsByClassName("note");

console.log(title.textContent);
console.log(firstNote.textContent);
console.log(allNotes.length);
console.log(paragraphs.length);
console.log(notesByClass.length);
```

**Explanation**

-   `getElementById("title")` selects the element with that ID.
-   `querySelector(".note")` selects the first match for a CSS selector.
-   `querySelectorAll(".note")` returns a static NodeList of all
    matches.
-   `getElementsByTagName("p")` selects elements by tag name.
-   `getElementsByClassName("note")` selects elements by class name.

Check that an element exists before using it when the selector might not
match:

``` javascript
const status = document.querySelector("#status");

if (status) {
  status.textContent = "Ready";
}
```

### 10.2 Change text and HTML

``` javascript
const message = document.getElementById("message");

message.textContent = "Your changes have been saved.";
```

**Explanation:** `textContent` treats the assigned value as text. It is
the safer choice when displaying user-provided content.

``` javascript
const details = document.getElementById("details");
details.innerHTML = "<strong>Training status:</strong> Active";
```

**Explanation:** `innerHTML` parses the assigned string as HTML. Only
use it with trusted or safely sanitised content; inserting untrusted
strings can create cross-site scripting (XSS) vulnerabilities.

### 10.3 Change attributes and styles

``` javascript
const link = document.querySelector("#documentation");
link.setAttribute("href", "https://developer.mozilla.org/");
link.setAttribute("target", "_blank");
link.setAttribute("rel", "noopener noreferrer");

const submitButton = document.querySelector("#submit");
submitButton.removeAttribute("disabled");

const heading = document.querySelector("h1");
heading.style.color = "darkblue";
heading.style.fontSize = "2rem";
```

**Explanation:** `setAttribute()` sets an HTML attribute and
`removeAttribute()` removes one. The `style` property can update
individual inline CSS properties. For larger style changes, toggling a
CSS class is often easier to maintain.

### 10.4 Use CSS classes for styling

``` html
<p id="status" class="status">Waiting</p>
```

``` css
.status {
  padding: 0.5rem;
  border-radius: 0.25rem;
}

.status.is-success {
  color: #14532d;
  background: #dcfce7;
}
```

``` javascript
const status = document.getElementById("status");
status.classList.add("is-success");
status.classList.remove("is-loading");
status.classList.toggle("is-highlighted");
```

**Explanation:** `classList.add()`, `remove()`, and `toggle()` let
JavaScript change presentation through CSS classes without scattering
style values through the script.

------------------------------------------------------------------------

## 11. Creating and Inserting Content

The DOM API provides methods for creating nodes and adding them to the
document.

### 11.1 `createElement()`, `createTextNode()`, and `appendChild()`

``` html
<ul id="skill-list"></ul>
```

``` javascript
const list = document.getElementById("skill-list");

const item = document.createElement("li");
const text = document.createTextNode("JavaScript");

item.appendChild(text);
list.appendChild(item);
```

**Explanation:** `createElement()` creates a new element node.
`createTextNode()` creates a text node. `appendChild()` attaches the new
node to the end of the parent's child list.

### 11.2 `insertBefore()`

``` javascript
const list = document.getElementById("skill-list");

const newItem = document.createElement("li");
newItem.textContent = "HTML";

const existingItem = list.firstElementChild;

if (existingItem) {
  list.insertBefore(newItem, existingItem);
} else {
  list.appendChild(newItem);
}
```

**Explanation:** `insertBefore(newItem, existingItem)` inserts the new
node immediately before the reference node. The reference node must be a
child of the parent on which the method is called.

### 11.3 Add and remove elements

``` javascript
const list = document.getElementById("skill-list");

const item = document.createElement("li");
item.textContent = "CSS";
item.className = "skill-item";
list.appendChild(item);

// Remove the item later
item.remove();
```

**Explanation:** Creating an element does not display it until it is
attached to the DOM. `remove()` removes a node from its parent.

### 11.4 `innerHTML` versus DOM node creation

``` javascript
const container = document.getElementById("container");

// Appropriate for a fixed, trusted HTML template:
container.innerHTML = "<p><strong>Ready</strong> to begin.</p>";

// Safer for text that may come from a user:
const userText = "<img src=x onerror=alert(1)>";
const paragraph = document.createElement("p");
paragraph.textContent = userText;
container.appendChild(paragraph);
```

**Explanation:** The second example displays the user string as text
rather than interpreting it as markup. Do not insert untrusted input
into `innerHTML`. When generating many elements, build the needed nodes
and append them efficiently rather than repeatedly rebuilding large
sections of the page.

------------------------------------------------------------------------

## 12. Browser Events

An event is an action or occurrence recognised by the browser.
JavaScript can respond to events such as clicks, key presses, form
submissions, page loading, and window resizing.

### 12.1 Common event types

  Event         Typical use
  ------------- ----------------------------------------
  `click`       A button or link is clicked
  `mouseover`   The pointer moves onto an element
  `mouseout`    The pointer moves away from an element
  `keydown`     A key is pressed
  `keyup`       A key is released
  `submit`      A form is submitted
  `load`        A resource or page finishes loading
  `resize`      The browser window changes size

### 12.2 Prefer `addEventListener()`

``` html
<button id="countButton">Click me</button>
<p id="countOutput">Clicked 0 times</p>
```

``` javascript
const button = document.getElementById("countButton");
const output = document.getElementById("countOutput");
let count = 0;

button.addEventListener("click", function (event) {
  count++;
  output.textContent = `Clicked ${count} times`;
  console.log(event.type); // "click"
});
```

**Explanation:** `addEventListener()` registers a handler without
overwriting other listeners attached to the same event. The handler
receives an event object with details about the event and its target.

### 12.3 Inline handler versus event listener

``` html
<!-- Works, but is not preferred for maintainable applications -->
<button onclick="alert('Clicked')">Inline handler</button>
```

Preferred approach:

``` html
<button id="saveButton">Save</button>
<script>
  document.getElementById("saveButton").addEventListener("click", () => {
    console.log("Save requested");
  });
</script>
```

**Explanation:** Inline handlers mix behaviour into HTML. Event
listeners keep behaviour in JavaScript and make it easier to register
multiple handlers and maintain the code.

### 12.4 Form submission and `preventDefault()`

``` html
<form id="registrationForm">
  <label for="email">Email</label>
  <input id="email" name="email" type="email" required>
  <button type="submit">Register</button>
  <p id="formMessage" aria-live="polite"></p>
</form>
```

``` javascript
const form = document.getElementById("registrationForm");
const emailInput = document.getElementById("email");
const formMessage = document.getElementById("formMessage");

form.addEventListener("submit", function (event) {
  event.preventDefault();

  const email = emailInput.value.trim();

  if (email === "") {
    formMessage.textContent = "Please enter your email.";
    return;
  }

  formMessage.textContent = `Registration submitted for ${email}.`;
});
```

**Explanation:** A form normally submits using the browser's default
behaviour. `preventDefault()` cancels that default action so the script
can handle the submission. Client-side checks improve user experience
but do not replace server-side validation.

### 12.5 Remove an event listener

``` javascript
function handleResize() {
  console.log(`Window width: ${window.innerWidth}`);
}

window.addEventListener("resize", handleResize);

// When the listener is no longer needed:
window.removeEventListener("resize", handleResize);
```

**Explanation:** To remove a listener, pass the same event type and the
same function reference used when registering it. An anonymous function
written separately cannot be matched to the original anonymous function.

### 12.6 Event propagation

Events can travel through the DOM during capture and bubbling. With the
usual `addEventListener("click", handler)` form, handlers normally run
during the bubbling phase.

``` html
<div id="parent">
  <button id="child">Click child</button>
</div>
```

``` javascript
document.getElementById("parent").addEventListener("click", () => {
  console.log("Parent handler");
});

document.getElementById("child").addEventListener("click", event => {
  console.log("Child handler");
  // event.stopPropagation(); // Uncomment to stop bubbling further
});
```

**Explanation:** Clicking the child normally logs the child handler and
then the parent handler. `stopPropagation()` stops the event from
continuing through the propagation path; use it only when that behaviour
is needed.

------------------------------------------------------------------------

## 13. String Methods: `match()`, `split()`, `trim()`, and `join()`

These methods are useful for cleaning, searching, and formatting text.

### 13.1 `trim()`

``` javascript
const rawName = "   Sara Ahmed   ";
const name = rawName.trim();

console.log(name); // "Sara Ahmed"
```

**Explanation:** `trim()` removes whitespace from the beginning and end
of a string. It does not remove spaces between words and does not modify
the original string.

### 13.2 `split()`

``` javascript
const csvLine = "Ali,Mona,Sara";
const names = csvLine.split(",");

console.log(names); // ["Ali", "Mona", "Sara"]
```

**Explanation:** `split(separator)` divides a string into an array using
the given separator. The separator is not included in the resulting
elements.

### 13.3 `join()`

``` javascript
const names = ["Ali", "Mona", "Sara"];
const displayNames = names.join(" | ");

console.log(displayNames); // "Ali | Mona | Sara"
```

**Explanation:** `join(separator)` combines array elements into a
string. It is the reverse-style companion to splitting a string into
pieces, although the operations are not always perfectly reversible if
separators also occur inside the data.

### 13.4 `match()` with a regular expression

``` javascript
const sentence = "Contact the team at support@example.com";
const emailMatch = sentence.match(/[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}/i);

console.log(emailMatch ? emailMatch[0] : "No email found");
// "support@example.com"
```

**Explanation:** `match()` searches a string using a regular expression.
It returns match information when a match is found, or `null` when there
is no match. This example is a simple pattern demonstration, not a
complete email-address validator.

### 13.5 Combine the methods

``` javascript
const rawInput = "  html, css, javascript  ";

const technologies = rawInput
  .trim()
  .split(",")
  .map(technology => technology.trim());

console.log(technologies); // ["html", "css", "javascript"]
console.log(technologies.join(" / ")); // "html / css / javascript"
```

**Explanation:** The input is trimmed, split at commas, and each
resulting item is trimmed to remove surrounding whitespace. Finally,
`join()` creates a formatted display string.

------------------------------------------------------------------------

## 14. Modern JavaScript (ES6+)

ECMAScript 2015 (often called ES6) introduced major syntax improvements
and features. Modern JavaScript includes these and many later additions.

### 14.1 Template literals

``` javascript
const name = "Mona";
const completedTasks = 7;
const totalTasks = 10;

const summary = `${name} completed ${completedTasks} of ${totalTasks} tasks.`;

console.log(summary);
// Mona completed 7 of 10 tasks.
```

**Explanation:** Template literals use backticks. `${...}` embeds a
variable or expression, and template literals can span multiple lines
without concatenating strings manually.

### 14.2 `map()`, `filter()`, and `reduce()`

``` javascript
const prices = [100, 200, 300];

const salePrices = prices.map(price => price * 0.9);
console.log(salePrices); // [90, 180, 270]

const affordable = prices.filter(price => price < 250);
console.log(affordable); // [100, 200]

const total = prices.reduce((sum, price) => sum + price, 0);
console.log(total); // 600
```

**Explanation:** `map()` transforms each value, `filter()` selects
values that meet a condition, and `reduce()` combines values into a
single result. These methods return new results and do not mutate the
original array in these examples.

### 14.3 Destructuring

``` javascript
const trainee = {
  name: "Omar",
  track: "Frontend"
};

const { name, track } = trainee;
console.log(name);  // Omar
console.log(track); // Frontend

const coordinates = [10, 20];
const [x, y] = coordinates;
console.log(x, y); // 10 20
```

**Explanation:** Destructuring extracts values from arrays or properties
from objects into variables with concise syntax.

### 14.4 Spread syntax

``` javascript
const baseSkills = ["HTML", "CSS"];
const extraSkills = ["JavaScript", "DOM"];

const allSkills = [...baseSkills, ...extraSkills];
console.log(allSkills); // ["HTML", "CSS", "JavaScript", "DOM"]

const original = { name: "Sara", active: true };
const updated = { ...original, active: false };

console.log(updated); // { name: "Sara", active: false }
```

**Explanation:** Spread syntax expands iterable values or enumerable
object properties. These examples make shallow copies; nested objects
are still shared references unless copied separately.

------------------------------------------------------------------------

## 15. Classes and Modules

### 15.1 Classes, constructors, methods, and instances

``` javascript
class Trainee {
  constructor(name, track) {
    this.name = name;
    this.track = track;
  }

  introduce() {
    return `I am ${this.name}, learning ${this.track}.`;
  }
}

const traineeOne = new Trainee("Mona", "JavaScript");
const traineeTwo = new Trainee("Ali", "Frontend");

console.log(traineeOne.introduce());
console.log(traineeTwo.introduce());
```

**Explanation**

-   `class` defines a blueprint for similar objects.
-   `constructor()` initialises each new instance.
-   `this.name` and `this.track` store instance-specific properties.
-   `introduce()` is a method available to instances.
-   `new` creates an instance of the class.

### 15.2 Exporting and importing modules

Use a simple project structure:

``` text
project/
├── index.html
├── main.js
└── math.js
```

**`math.js`**

``` javascript
export const PI = 3.14159;

export function add(a, b) {
  return a + b;
}
```

**`main.js`**

``` javascript
import { PI, add } from "./math.js";

console.log(PI);
console.log(add(2, 3)); // 5
```

**`index.html`**

``` html
<!doctype html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>ES Modules</title>
  <script type="module" src="main.js"></script>
</head>
<body>
  <h1>JavaScript Modules</h1>
</body>
</html>
```

**Explanation:** `export` makes values available to other modules, and
`import` brings them into the current module. The HTML script uses
`type="module"`. When browser module loading is blocked by local-file
restrictions, run the project through a local development server.

------------------------------------------------------------------------

## 16. Integrated Practice Project

Build a small **Skills List** application. It combines variables,
functions, DOM selection, content creation, events, arrays, and safe
text handling.

### Requirements

-   Add a skill using a text field and button.
-   Ignore blank input.
-   Display each skill as a list item.
-   Allow a skill to be removed.
-   Use `textContent` for user-entered text rather than injecting it as
    HTML.

### `index.html`

``` html
<!doctype html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Skills List</title>
  <script src="app.js" defer></script>
</head>
<body>
  <main>
    <h1>My Skills</h1>

    <form id="skillForm">
      <label for="skillInput">Skill name</label>
      <input id="skillInput" name="skill" type="text" required>
      <button type="submit">Add skill</button>
    </form>

    <p id="feedback" aria-live="polite"></p>
    <ul id="skillList"></ul>
  </main>
</body>
</html>
```

### `app.js`

``` javascript
const form = document.getElementById("skillForm");
const input = document.getElementById("skillInput");
const list = document.getElementById("skillList");
const feedback = document.getElementById("feedback");

const skills = [];

function renderSkill(skill) {
  const item = document.createElement("li");
  const skillText = document.createElement("span");
  const removeButton = document.createElement("button");

  skillText.textContent = skill;
  removeButton.textContent = "Remove";
  removeButton.type = "button";
  removeButton.setAttribute("aria-label", `Remove ${skill}`);

  removeButton.addEventListener("click", () => {
    const index = skills.indexOf(skill);

    if (index !== -1) {
      skills.splice(index, 1);
    }

    item.remove();
    feedback.textContent = `${skill} removed.`;
  });

  item.appendChild(skillText);
  item.appendChild(document.createTextNode(" "));
  item.appendChild(removeButton);
  list.appendChild(item);
}

form.addEventListener("submit", event => {
  event.preventDefault();

  const skill = input.value.trim();

  if (skill === "") {
    feedback.textContent = "Enter a skill before adding it.";
    return;
  }

  skills.push(skill);
  renderSkill(skill);
  feedback.textContent = `${skill} added.`;
  form.reset();
  input.focus();
});
```

**How it works**

1.  The script selects the form, input, list, and feedback elements.
2.  The `submit` listener prevents a page reload and reads the trimmed
    input.
3.  Blank input is rejected.
4.  A valid skill is stored in the `skills` array.
5.  `renderSkill()` creates DOM nodes and adds them to the list.
6.  Each remove button deletes the corresponding list item and removes
    the skill from the array.

**Extension tasks**

-   Prevent duplicate skill names.
-   Add a counter showing the number of skills.
-   Add a "Clear all" button.
-   Show a message when the list is empty.
-   Move rendering into a function that rebuilds the list from the
    `skills` array.
-   Add CSS classes for success and error feedback.

**Engineering note:** This teaching example uses the skill text as the
lookup value. If duplicate values are allowed, removing by text can
remove the wrong matching entry from the array. A production-quality
version should give each item a unique identifier and use that
identifier for updates and deletion.

------------------------------------------------------------------------

## 17. Engineering Best Practices

1.  **Prefer `const`, then `let`.** Use `var` only when maintaining
    older code or when its behaviour is specifically required.
2.  **Use strict comparisons.** Prefer `===` and `!==` unless type
    coercion is intentional.
3.  **Keep concerns separate.** Put substantial JavaScript in external
    `.js` files and styling in CSS files.
4.  **Use descriptive names.** For example, `totalPrice` is clearer than
    `tp`.
5.  **Write small functions.** Each function should have a clear
    responsibility.
6.  **Prefer `addEventListener()`.** Avoid inline event attributes in
    maintainable projects.
7.  **Handle missing elements.** A selector can return `null`; check
    before accessing properties.
8.  **Treat input as untrusted.** Use `textContent` for text. Avoid
    inserting user input through `innerHTML`.
9.  **Validate on the server too.** Browser-side validation can be
    bypassed and is not a security boundary.
10. **Avoid unnecessary DOM updates.** Batch changes where practical,
    especially for large lists.
11. **Know which methods mutate data.** For example, `splice()`,
    `reverse()`, and `sort()` modify the original array; `map()`,
    `filter()`, and `slice()` return new arrays.
12. **Use Developer Tools.** Inspect console errors, element state,
    event behaviour, and network activity while debugging.
13. **Make interfaces accessible.** Use labels for inputs, semantic
    HTML, and appropriate status messages for dynamic feedback.
14. **Test edge cases.** Try empty input, unexpected values, repeated
    clicks, missing elements, and large collections.

------------------------------------------------------------------------

## Quick Reference

  ----------------------------------------------------------------------------------
  Need                                Use
  ----------------------------------- ----------------------------------------------
  Declare a value that should not be  `const`
  reassigned                          

  Declare a value that must be        `let`
  reassigned                          

  Select one element by ID            `document.getElementById("id")`

  Select the first CSS selector match `document.querySelector("selector")`

  Select all CSS selector matches     `document.querySelectorAll("selector")`

  Read or write text                  `element.textContent`

  Read or write HTML markup           `element.innerHTML` --- only with
                                      trusted/sanitised markup

  Create an element                   `document.createElement("tag")`

  Add a node at the end               `parent.appendChild(child)`

  Register an event listener          `element.addEventListener("click", handler)`

  Stop default event behaviour        `event.preventDefault()`

  Repeat over iterable values         `for...of`

  Transform an array                  `array.map(callback)`

  Select matching array values        `array.filter(callback)`

  Combine array values into one       `array.reduce(callback, initialValue)`
  result                              

  Remove surrounding whitespace       `string.trim()`

  Split a string into an array        `string.split(separator)`

  Combine an array into a string      `array.join(separator)`

  Search text with a regular          `string.match(regex)`
  expression                          

  Create a formatted string           `` `Hello, ${name}` ``

  Define a class                      `class ClassName { ... }`

  Import module exports               `import { value } from "./file.js"`
  ----------------------------------------------------------------------------------

------------------------------------------------------------------------

## Learning Check

Before completing Unit 11, make sure you can:

-   [ ] Explain the roles of HTML, CSS, JavaScript, and the DOM.
-   [ ] Declare variables and identify common data types.
-   [ ] Use operators and conditional statements to make decisions.
-   [ ] Choose an appropriate loop and explain `break` versus
    `continue`.
-   [ ] Write functions using declarations, expressions, and arrow
    syntax.
-   [ ] Use arrays, objects, and common array methods.
-   [ ] Select DOM elements and safely update content and attributes.
-   [ ] Create, insert, and remove DOM elements.
-   [ ] Attach and remove event listeners and explain the event object.
-   [ ] Use `match()`, `split()`, `trim()`, and `join()` for text
    processing.
-   [ ] Use template literals, destructuring, spread syntax, classes,
    and modules.
-   [ ] Explain why untrusted content should not be inserted into
    `innerHTML`.

------------------------------------------------------------------------

**Training reference:** Unit 11 --- JavaScript and the DOM. This README
reorganises the unit topics into an engineer-focused reference with
runnable examples and explanations. It expands the source material with
practical implementation guidance where needed.
