## The JS Engine

- At the center of every runtime sits the **JavaScript engine**.  Examples include **V8 (Chrome and Node.js)**, **SpiderMonkey (Firefox)**, and **JavaScriptCore (Safari)**.

- The engine is responsible for the core language mechanics:
	- parsing JavaScript source code
	- compiling or interpreting the code
	- executing functions on the **call stack**
	- managing memory (stack and heap)
	- garbage collection
	- implementing the language specification (ECMAScript)

- What the engine does _not_ provide are things like timers, networking, or the DOM. Those belong to the runtime.

```txt
Simplified View -- 

Your JS code
     ↓
JavaScript Engine
     ↓
Machine instructions
```

## Runtime APIs

- Around the engine sits a collection of APIs supplied by the environment called **host APIs**.
- They allow JavaScript to interact with the system it is running on.

- Examples in the browser include:
	- timers (`setTimeout`, `setInterval`)
	- networking (`fetch`, `XMLHttpRequest`)
	- DOM manipulation (`document`, `window`)
	- events (`addEventListener`)
	- storage (`localStorage`, `IndexedDB`)

```js
setTimeout(() => {
  console.log("Timer finished")
}, 1000)
```

- The JavaScript engine does **not** manage time. Instead:
	1. the engine registers the timer
	2. the browser runtime tracks the timer
	3. once the timer expires, the callback is queued
- The runtime handles the waiting.

## The Event Loop

- The **event loop** is the coordinator between the runtime and the engine.
- The event loop ensures that asynchronous callbacks eventually run.

- Its job is simple but fundamental - **If the call stack is empty, take the next task from the queue, and push it onto the stack.**

```txt
JS engine runs synchronous code
↓
async task delegated to runtime
↓
runtime finishes task
↓
callback enters queue
↓
event loop moves callback to stack
```

- Without the event loop, asynchronous tasks would never execute.

## Task Queues

- When asynchronous work completes, callbacks do not run immediately. They are placed into queues.
- There are two primary queues -
### Macrotask Queue

- Used for -
	- `setTimeout`
	- `setInterval`
	- DOM events
	- network callbacks
	- message channels
- The callback goes into the macrotask queue for each one.

### Microtask Queue

- Used for:
	- `Promise.then`
	- `queueMicrotask`
	- `MutationObserver`
- Microtasks are processed **before macrotasks**.
### Execution order

```txt
1. synchronous code  
2. microtasks  
3. macrotasks
```
- This distinction explains many async ordering behaviors.

## Browser Runtime Components

- A browser runtime contains several major subsystems.
### DOM Engine
- This manages the **Document Object Model**, which represents the web page structure.
- `document.getElementById("btn")` - The DOM engine translates these operations into changes in the browser’s rendering system.
### Rendering Engine
- Responsible for displaying the page.
- It handles - layout, painting, compositing.
- JavaScript execution can block rendering. If the call stack is busy, the browser cannot update the screen.
### Networking Layer
- Handles HTTP requests.
- `fetch("/api/users")` - The networking subsystem performs the request asynchronously and notifies the runtime when it finishes.
### Timer System
- Manages timers.
- `setTimeout(callback, 2000)` - The timer system tracks elapsed time and triggers the callback when appropriate.
### Event System
- Browsers constantly listen for user interactions: clicks, keyboard input, scrolling, pointer movement
- `button.addEventListener("click", handler)` - When a click occurs, the handler is placed into the task queue.

## Message Passing Between Runtime and Engine

- The communication between the runtime and engine follows a predictable cycle. This loop runs continuously while the application is active -

```txt
JS registers async operation
↓
runtime performs operation
↓
operation finishes
↓
callback queued
↓
event loop schedules callback
↓
JS engine executes callback
```

## Important Conclusion

- JavaScript itself is **not asynchronous**. The language is synchronous by design.
- Asynchronous behavior emerges from:
	- the runtime environment    
	- task queues    
	- the event loop
- When developers say “JavaScript is asynchronous,” what they really mean is that **JavaScript runs in an asynchronous runtime system**.

