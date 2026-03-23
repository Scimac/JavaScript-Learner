## The foundation of the loop

- Node.js runs JavaScript on **one main thread**. Only one piece of JS code executes at a time.
- But Node can still handle many operations simultaneously because it **delegates long operations to the runtime (libuv or the OS)** and later schedules their callbacks.

- The event loop’s job is simple:
```
If the call stack is empty,  
take the next callback from a queue  
and execute it.
```
Conceptually the loop behaves like:
```
while (application_is_running) {  
    process_callbacks()  
}
```
But internally Node’s loop is more structured.

## Where the Loop lives

- The event loop is implemented inside **libuv**, the C library Node uses for async I/O.
- libuv manages:
	- I/O events
	- timers
	- thread pool tasks
	- event scheduling

```
	JavaScript Code  
		↓  
	V8 Engine  
		↓  
	Node APIs  
		↓  
	libuv Event Loop  
		↓  
	Operating System
```

## The Phases of the Event Loop

- Unlike browsers, Node’s event loop has **six distinct phases**. Each phase processes a specific queue of callbacks.

```txt
┌───────────────────┐
│      timers       │
├───────────────────┤
│ pending callbacks │
├───────────────────┤
│   idle / prepare  │
├───────────────────┤
│       poll        │
├───────────────────┤
│       check       │
├───────────────────┤
│   close callbacks │
└───────────────────┘
```
- The loop cycles through these phases continuously.

### 1. Timers Phase

- This phase executes callbacks scheduled by:
	- `setTimeout`
	- `setInterval`
- 
```js
setTimeout(() => {
  console.log("timer finished")
}, 1000)
```

- A timer does **not run exactly after the delay**. If the event loop is busy, the callback may run later.
```txt
minimum set delay passes
↓
callback becomes eligible
↓
event loop runs it in timers phase
```

### 2. Pending Callbacks Phase

- This phase executes callbacks from certain **system operations**.
- Examples include: TCP errors, some networking callbacks
- Most application developers rarely interact with this phase directly.
- But it exists to process **deferred system-level callbacks**.
### 3. Idle / Prepare Phase

- This phase is mostly used internally by Node.
- It allows libuv to perform **internal housekeeping tasks** before moving to the next stage.
- Application code typically does not interact with this phase.
### 4. Poll Phase (The Most Important Phase)

- The **poll phase** is the heart of the Node event loop.
- This phase:
	- retrieves new I/O events
	- executes I/O callbacks
	- may wait for new events if none exist
- Examples of operations handled here:
	- file system callbacks
	- database responses
	- network responses

```js
const fs = require("fs")

fs.readFile("data.txt", () => {
  console.log("file read complete")
})
```

```txt
JS thread registers read request
↓
libuv thread pool performs file read
↓
result returned
↓
callback enters poll queue
↓
event loop executes callback
```
- The poll phase can also **block temporarily waiting for I/O**, which makes Node efficient.

### 5. Check Phase

- This phase executes callbacks scheduled with: `setImmediate()`

```
setImmediate(() => {  
  console.log("immediate callback")  
})
```
- `setImmediate` callbacks always run in the **check phase**, after the poll phase.

### 6. Close Callbacks Phase

- This phase executes cleanup callbacks for closed resources.
- Examples - `socket.on("close", handler)` or `server.close()`
- When a resource closes unexpectedly, the callback runs here.

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
	- `process.nextTick`

- Microtasks are processed **before macrotasks**.
- These do **not belong to a phase**. Instead they run **immediately after the currently executing callback finishes and before the event loop proceeds to the next phase**.
### `process.nextTick` Queue

- Node has a special microtask queue for: `process.nextTick()`.
```
process.nextTick(() => {  
  console.log("nextTick")  
})
```

- This queue runs **before the promise microtask queue**.

Priority order:
```
process.nextTick  
↓  
Promise microtasks  
↓  
event loop phases
```
### Execution order

```txt
1. synchronous code  
2. nextTick
3. microtasks  
4. macrotasks
```
- This distinction explains many async ordering behaviors.

## Poll vs Check Example

- A famous Node example demonstrates the difference between `setTimeout` and `setImmediate`.
```js
const fs = require("fs")  
  
fs.readFile(__filename, () => {  
  setTimeout(() => console.log("timeout"), 0)  
  setImmediate(() => console.log("immediate"))  
})
```

- Output:
```
immediate  
timeout
```

- Why? Inside an I/O callback:
```
poll phase finishes  
↓  
check phase runs (setImmediate)  
↓  
timers phase next iteration
```

## Visualising the whole loop

```
Event Loop Iteration

timers
   ↓
pending callbacks
   ↓
idle / prepare
   ↓
poll
   ↓
check
   ↓
close callbacks
```

- Between each callback execution:
```
nextTick 
↓
microtasks
↓
next phase
```
