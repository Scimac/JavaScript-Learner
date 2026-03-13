## 1. Execution Context

Every piece of JS runs inside an execution context.

Structure:
```
Execution Context
 ├─ Variable Environment
 ├─ Lexical Environment
 └─ this Binding
```

## 2. Call Stack

JavaScript executes functions through a stack structure.

```
main()  
 → functionA()  
   → functionB()
```
Understanding this explains recursion and stack overflow.

## 3. Lexical Environment

Variables are resolved through scope chains.

```
inner scope  
   ↓  
outer scope  
   ↓  
global scope
```

Closures depend on this.

## 4. Hoisting Mechanism

During the **creation phase**, JavaScript registers variables and functions before execution.

Key rules:

- functions hoist fully
    
- `var` hoists as `undefined`
    
- `let/const` stay in TDZ

## 5. Closures

A function retaining access to its lexical scope even after the outer function finishes.

Closures power:

- module patterns
    
- private state
    
- React hooks
    
- memoization

## 6. Prototype Chain

JavaScript inheritance mechanism.

```
object  
  ↓  
prototype  
  ↓  
prototype  
  ↓  
null
```

Property lookup walks this chain.

## 7. `this` Binding Rules

JavaScript determines `this` through four rules:

1. default binding
    
2. implicit binding
    
3. explicit binding (`call/apply/bind`)
    
4. `new` binding
    

Arrow functions override this behavior.


## 8. Iterator Protocol

Defines how objects become iterable.

Requirement:

`obj[Symbol.iterator]()`

Returns:

`{ value, done }`

Used by:

- `for...of`
    
- spread operator
    
- destructuring


## 9. Generator Execution Model

Generators introduce **pausable execution**.

Internally they maintain a **state machine**:

```
suspendedStart  
suspendedYield  
executing  
completed
```


## 10. Event Loop

The heart of asynchronous JavaScript.

Structure:

```
Call Stack  
   ↓  
Microtask Queue  
   ↓  
Macrotask Queue
```

Execution order:

```
sync → microtasks → macrotasks
```

## 11. Promise Microtask Scheduling

Promises resolve into the **microtask queue**.

This is why:

```
Promise.then
```

runs before:

```
setTimeout
```

## 12. Memory Model & Garbage Collection

JavaScript memory model:

```
Stack → primitives  
Heap → objects
```

Garbage collection uses **mark-and-sweep**.

Understanding this helps avoid memory leaks.

# The Insight That Changes Everything

Most JavaScript features are combinations of these internals.

For example:

### `async/await`

is essentially:

```
Promises  
+ Generators  
+ Microtasks
```

---

### `for...of`

is:

```
Iterator protocol  
+ Symbol.iterator
```

---

### React Hooks

are built on:

```
closures  
+ execution order
```