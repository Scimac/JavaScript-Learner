## JavaScript Execution Model

- At its core, JavaScript executes code through a **single-threaded execution model built around a call stack and execution contexts**. 
- Every line of JavaScript you run participates in this system.

```txt
  Source Code
     ↓
  Parser
     ↓
  Execution Context Creation
     ↓
  Call Stack Execution
```

## Single-Threaded Nature of JavaScript

- JavaScript executes on **one main thread** i.e **Only ONE piece of JS code is executing.**
```js
function task1() {
  console.log("Task 1 start")
  console.log("Task 1 end")
}

function task2() {
  console.log("Task 2")
}

task1()
task2()

// output : 
// Task 1 start
// Task 1 end
// Task 2
```

- Even if multiple functions exist, they **do not run simultaneously**. They run **one after another** on the call stack.

> This explains why synchronous blocking code freezes applications.

- `while(true) {}` - This infinite loop occupies the only thread and prevents any other code from executing.

## Execution Context

- Every piece of JavaScript runs inside an **Execution Context**. 
- An execution context is essentially the **environment in which JavaScript code is evaluated and executed** (like a scope).
- There are three main types of execution context - 

### Global Execution Context

- When a JavaScript program starts, the engine creates a **global execution context**.

- This context:
	- creates the global object (`window` in browsers, `global` in Node)
	- defines the global `this`	    
	- stores global variables and functions

```js
var x = 10

function foo() {
  console.log("hello")
}
```

During global execution context creation, it create these properties on global object - 

```txt
x → undefined
foo → function reference
```

### Function Execution Context

- Every time a function is called, a **new execution context** is created.

```js
function greet(name) {
  console.log("Hello", name)
}

greet("Makarand")
```

Execution steps of the same are - 

```txt
Global Context
    ↓
Call greet()
    ↓
New Function Execution Context
```

- Inside this context the engine stores:
	- parameters
	- local variables
	- `this` binding
	- scope references
- Once the function finishes, the context is removed.
- This hierarchy can be seen in the stack trace while debugger is hit or error is logged in the inspect console.

### `eval` Execution Context 

- **Eval Execution Context** is a special environment created when code is executed inside the built-in global `eval()` function.
- Code inside a _direct_ `eval()` call has access to the local scope (lexical environment) in which it was invoked, which is a key reason for its security and performance issues.

```js
console.log(eval("2 + 2"));
// Expected output: 4

console.log(eval(new String("2 + 2")));
// Expected output: 2 + 2

console.log(eval("2 + 2") === eval(new String("2 + 2")));
// Expected output: false
```

- The argument passed to this function is dynamically parsed and executed as JavaScript. APIs like this are known as [injection sinks](https://developer.mozilla.org/en-US/docs/Web/API/Trusted_Types_API#concepts_and_usage), and are potentially a vector for [cross-site-scripting (XSS)](https://developer.mozilla.org/en-US/docs/Web/Security/Attacks/XSS) attacks.
- You can mitigate this risk by always passing [`TrustedScript`](https://developer.mozilla.org/en-US/docs/Web/API/TrustedScript) objects instead of strings and [enforcing trusted types](https://developer.mozilla.org/en-US/docs/Web/API/Trusted_Types_API#using_a_csp_to_enforce_trusted_types).

- JavaScript engines cannot optimize code run inside `eval()` as effectively as normal code, as the content is dynamic. 
- This forces the browser to perform long, expensive variable lookups and potentially re-evaluate generated machine code.

### Components of an Execution Context

#### Variable Environment
- Stores:
	- function declarations
	- `var` variables
	- parameters

```js
function test(a) {
  var b = 20
}```

```txt
Variable environment contains:

a → argument value
b → undefined (initially)
```

#### Lexical Environment

- Tracks **scope relationships**. Every environment keeps a reference to its **outer environment**.

```js
function outer() {
  let a = 10

  function inner() {
    console.log(a)
  }

  inner()
}
```

- Environment chain: `inner → outer → global`. When `inner` needs `a`, the engine searches this chain.
- This mechanism is called the **scope chain**.
#### `this` Binding

- Each execution context determines the value of `this`.

```js
const user = {
  name: "Makarand",
  greet() {
    console.log(this.name)
  }
}

user.greet()
```

- Inside `greet`: `this → user`, `this` binding depends on **how a function is called**, not where it is defined.
- we can change `this` binding using `bind` and `apply`.

### The Call Stack

- The **Call Stack** manages execution contexts. It works exactly like a stack data structure.
- `Last In  --> First Out`

```js
function a() {
  b()
}

function b() {
  c()
}

function c() {
  console.log("running")
}

a()
```

```txt
Call Stack

Global
  ↓
a()
  ↓
b()
  ↓
c()
  ↓
c removed after c finish
b resumes
  ↓
b removed
a resumes
  ↓
a removed
```

### Stack Frames

- Each function call creates a **stack frame**. A stack frame contains:
```txt
Function arguments
Local variables
Execution pointer
Return address
```

```js
function add(a,b) {
  return a + b
}

add(2,3)
```

```txt
// Stack frame for `add`:
a → 2
b → 3
return → global context
```

### Memory Model

- JavaScript uses two primary memory regions. `Stack, Heap`
- Stack Stores:
	- primitives
	- execution contexts
	- function calls
- Heap Stores:
	- objects
	- arrays
	- functions

```js
// 1. Primitive value (string) stored on the stack
let name = 'John';
// name variable and the string 'John' are on the stack.

// 2. Object (reference type) stored in the heap, with a reference in the stack
const person = {
  name: 'Jane',
  age: 29,
};
// 'person' variable is on the stack, holding a memory address (reference)
// that points to the actual object data {'name': 'Jane', 'age': 29} in the heap.

// 3. Modifying a primitive
name = 'John Doe';
// The original 'John' in the stack is not changed; new memory is allocated
// for 'John Doe' on the stack, and the 'name' variable's reference is updated
// to the new location.

// 4. Modifying an object property via its reference
person.age = 30;
// The 'person' reference on the stack remains the same, but the data
// in the heap is modified.

// 5. Creating a new reference to an existing object
const husband = person;
// 'husband' is a new variable on the stack, but it holds the *same*
// reference (memory address) as 'person', pointing to the same object
// in the heap.

// 6. Demonstrating reference behavior
husband.name = 'Paul';
// The 'person' object's name is now also 'Paul' because both variables
// reference the same underlying object in the heap.

// 7. Memory deallocation (Garbage Collection hint)
let largeArray = new Array(1000000).fill('data');
// largeArray object stored in the heap.
largeArray = null;
// The reference from the stack is lost (set to null), making the large array
// in the heap unreachable and eligible for garbage collection.
```
- `obj` reference lives on stack, actual object lives in heap.

## Two Phases of Execution

- When JavaScript executes code, each execution context goes through two phases.
```txt
1. Creation Phase
2. Execution Phase
```

- During Creation Phase phase the engine:
	1. creates variable environment
	2. sets up scope chain
	3. determines `this`
	4. hoists declarations
- Execution phase executes the code line-by-line according to the environment created.

```js
console.log(x)
var x = 10
```

- Creation phase: 
	- `x → undefined`
- Execution phase: 
	- `console.log(undefined)`
	- `x = 10`

### Hoisting

- Hoisting occurs because declarations are processed during the creation phase.
#### Function Hoisting

- Works because the function is hoisted.
```js
sayHello()

function sayHello() {
  console.log("hello")
}
```

#### Variable Hoisting

- `let` and `const`. These are hoisted but not initialized. This creates the **Temporal Dead Zone**. 
- The **Temporal Dead Zone (TDZ)** in JavaScript is a period of time where variables declared with `let`, `const`, or `class` keywords exist within their scope but cannot be accessed or assigned a value. 
- Attempting to access a variable in the TDZ results in a `ReferenceError`.

```js
console.log(a)
let a = 10

// ReferenceError
```

### Scope Chain

- When JavaScript looks up variables, it follows the **scope chain**, the hierarchical structure used by the JavaScript engine to look up variables and determine their accessibility when they are referenced in the code.
- Resolution order: `inner → outer → global`

### Closures

- That bring us to Closures. They happen when a function retains access to variables from its lexical environment.

```js
function counter() {
  let count = 0

  return function() {
    count++
    console.log(count)
  }
}

const c = counter()

c()
c()

// output: 
// 1
// 2
```

- Even though `counter()` finished, `count` persists. Because the inner function **keeps the lexical environment alive**. 
- Closures are a direct consequence of the execution model.

### Recursion and Stack Overflow

- A **stack overflow** occurs when the recursion goes too deep, exhausting the limited memory allocated for the call stack by the JavaScript engine (which varies by browser).

```js
function recurse() {
  recurse()
}

recurse()

// o/p
// Maximum call stack exceeded
```

### Blocking the Call Stack

- Any long synchronous task blocks the stack. During the loop nothing else can execute.
- This is why async mechanisms exist.

```js
function heavy() {
  for(let i = 0; i < 1e9; i++) {}
}

console.log("start")
heavy()
console.log("end")

// o/p

// start  
// (wait long time)  
// end
```

## Summary

- Because the stack is single-threaded:
```txt
Long tasks block execution.
```
- So JavaScript relies on:
```txt
Event Loop  
Queues  
Promises
```
- to schedule work without blocking the stack.

- Everything else in JavaScript builds on this structure.
```txt
JavaScript Engine

Execution Contexts
      ↓
Call Stack
      ↓
Scope Chain
      ↓
Memory (Stack + Heap)
```
