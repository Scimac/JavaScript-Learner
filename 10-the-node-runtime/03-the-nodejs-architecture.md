## Node JS Architecture

- Node.js is not just “JavaScript running on a server.” 
- It is a **runtime system built around the V8 engine and an asynchronous I/O architecture**.

```txt
Application Code (JavaScript)
		│
		▼
	Node.js APIs
		│
		▼
		V8
		│
		▼
	   libuv
		│
		▼
 Operating System
```

- Nodejs is best at handling high number of requests, but not great at handling complex calculations. They are often delegated.
## V8 JavaScript Engine

- Node.js uses **Google’s V8 engine**.
- V8 is responsible for:
	- parsing JavaScript
	- compiling JavaScript
	- executing code
	- managing memory
	- garbage collection
	- maintaining the call stack

```js
function add(a, b) {  
  return a + b  
}  
  
add(1, 2)
```

- V8 will:
	1. parse the code
	2. compile it
	3. execute it
	4. manage memory allocation

- But V8 **cannot do file operations, networking, or timers**. That’s where Node’s architecture comes in.

## Node.js APIs (Bindings Layer)

- Node provides APIs written in **C++ bindings** that connect JavaScript code to system-level functionality.

```txt
fs
http
crypto
dns
stream
process
timers
```

Example -- 

```js
const fs = require("fs")

fs.readFile("file.txt", (err, data) => {
  console.log(data)
})
```

- The Node API layer translates **JavaScript requests into native operations**.

```txt
JS Code (V8)
  ↓
Nodejs fs module
  ↓
C++ bindings
  ↓
libuv (lib that runs event loop)
  ↓
 OS
```

## libuv (The Core of Node.js)

- The most important component in Node architecture is **libuv**.
- libuv is a **C library responsible for asynchronous I/O operations**.
- It provides:
	- event loop implementation
	- asynchronous file operations
	- thread pool
	- networking
	- timers
	- OS interaction

- This library is what allows Node to be **non-blocking** OR **Asynchronous**.

## The Event Loop

- libuv implements Node’s **event loop**.
- The event loop continuously checks for tasks to execute.

- Conceptually:

```js
while (app_is_running) {  
    process_callbacks()  
}
```

- For now the important idea is - `callbacks do not execute immediately , they are queued and executed later`. 
- Check the event loop section for more info.

## Thread Pool

- Even though JavaScript runs on **one thread**, Node internally uses a **thread pool** for certain operations.
- Default size: `4 threads` It can be configured via:  `UV_THREADPOOL_SIZE`.
- Operations that use the thread pool include:
	- file system operations
	- DNS lookup
	- crypto operations
	- compression
	- some database drivers

```js
fs.readFile("data.txt", callback)
```

```txt
JS thread → request operation  
libuv → sends work to thread pool  
thread performs work  
callback queued
```

- The main JavaScript thread remains free.

## OS Kernel Interaction

- Some operations do **not use the thread pool**. Instead they use **OS-level asynchronous I/O**
- Eg - network requests, TCP sockets, HTTP server

```js
const http = require("http")

http.createServer((req, res) => {
  res.end("Hello")
}).listen(3000)
```

```txt
Node registers socket with OS
OS listens for connections
When connection arrives
callback queued
```

- This allows Node to handle **thousands of connections efficiently**.

## Callback Queue

- When asynchronous operations finish, their callbacks are placed in **queues**. 
- These callbacks are later executed by the **event loop**.

```txt
file read complete
timer finished
network response
incoming HTTP request
```

## Timers System

- Node implements timers through libuv.
- Timers are not guaranteed to run exactly at the specified time; they run **after the delay once the event loop is ready**.

```js
setTimeout(() => {
  console.log("timer done")
}, 1000)
```

```txt
JS registers timer
libuv stores timer
timer expires
callback pushed to queue
```

## Modules System

- Node provides a module system to organize code.
- Historically it supported only `CommonJS` - `const fs = require("fs")`, Now Node also supports `ES Modules` - `import fs from "fs"`
- Modules are loaded through Node’s **module loader** which resolves file paths and dependencies.

## Streams

- Streams are a core architectural concept in Node.
- They allow processing **large data without loading everything into memory**.
- Streams are heavily used in networking and file operations.

```js
const fs = require("fs")

fs.createReadStream("bigfile.txt")
```

Types of streams:
```
Readable  
Writable  
Duplex  
Transform
```

## EventEmitter System

- Node uses an **event-driven architecture**.
- Many Node modules extend the `EventEmitter` class.

```js
const EventEmitter = require("events")

const emitter = new EventEmitter()

emitter.on("event", () => {
  console.log("event triggered")
})

emitter.emit("event")
```

- This pattern powers:
	- HTTP servers  
	- Streams  
	- Process signals  
	- Sockets

## Memory Model

- Node shares the JavaScript memory model:
```
Stack → execution contexts  
Heap → objects
```

- However, long-running Node servers must manage memory carefully to avoid leaks.
- Common leak causes:
	- unclosed streams  
	- retained closures  
	- event listeners not removed

## Why Node Scales Well

- Traditional servers often create **one thread per request**.
- Example model: `Request → Thread`
- Node uses: Single thread  Event-driven architecture  Non-blocking I/O
```
Request  
  ↓  
Event loop  
  ↓  
Async operation  
  ↓  
Callback
```

- This allows handling **tens of thousands of concurrent connections**.

## Big Picture

```txt
Client Request
      ↓
OS Network Layer
      ↓
	libuv
      ↓
  Event Loop
      ↓
Node HTTP module
      ↓
  JS Callback
      ↓
 Response sent
```

- This cycle repeats for every request.
