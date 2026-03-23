- When asynchronous work finishes, its callback is not executed immediately. 
- Instead, it is placed into a queue. In Node (and browsers), those queues fall into two broad categories -

## Macrotasks

- Macrotasks (sometimes called **tasks**) represent the main queues that the event loop cycles through in its phases.
- Typical sources of macrotasks include:
	- `setTimeout`
	- `setInterval`
	- I/O callbacks (file reads, network responses)
	- `setImmediate`
	- DOM events (in browsers)

```js
setTimeout(() => {
  console.log("timer")
}, 0)
```
- This callback is scheduled as a **macrotask**. It will run in the **timers phase** of the event loop.

## Microtasks

- Microtasks are a special queue of high-priority callbacks that run **immediately after the currently executing JavaScript finishes**, before the event loop proceeds to the next macrotask phase.
- Typical sources:
	- `Promise.then()`
	- `Promise.catch()`
	- `Promise.finally()`
	- `queueMicrotask()`

```js
Promise.resolve().then(() => {
  console.log("microtask")
})
```
- This callback enters the **microtask queue**.

## Node’s Extra Microtask Queue

- This queue runs **before the Promise microtask queue**.

```js
process.nextTick queue
```

```txt
process.nextTick  
↓  
Promise microtasks  
↓  
Event loop phases (macrotasks)
```

## The Execution Rule

```txt
After each synchronous operation or callback,
all microtasks are executed
before continuing the event loop.
```

- This means the extra-micro and microtask queue is **completely drained** before moving on.
## Microtask Starvation

- Because microtasks run before returning to the event loop, repeatedly scheduling microtasks can prevent other tasks from executing.

```js
function loop() {  
  process.nextTick(loop)  
}  
  
loop()
```

- The event loop never proceeds to other phases. This is called **event loop starvation**.
- It is why `process.nextTick()` should be used carefully.

- Microtasks always run **before the next macrotask**.

```js
setTimeout(() => console.log("timeout"))

Promise.resolve().then(() => console.log("promise"))

// output - 
// promise  
// timeout
```

## The Real Cycle

```txt
execute synchronous code
↓
run all process.nextTick callbacks
↓
run all promise microtasks
↓
enter event loop phases
↓
execute one macrotask
↓
run microtasks again
↓
continue loop
```

- This completes one loop of the event loop cycle.

## Examples

### Basic problem

```js
console.log("A")

setTimeout(() => console.log("B"), 0)

Promise.resolve().then(() => console.log("C"))

console.log("D")

// op
// A
// D
// C
// B
```

- synchronous code runs → `A`, `D`
- promise callback enters microtask queue
- event loop drains microtasks → `C`
- timer callback runs later → `B`

### Multiple Microtasks

```js
Promise.resolve().then(() => console.log("1"))
Promise.resolve().then(() => console.log("2"))
Promise.resolve().then(() => console.log("3"))

// op
// 1
// 2
// 3
```
- All microtasks run **in the order they were queued**.

### Nested Microtasks

```js
Promise.resolve().then(() => {
  console.log("first")

  Promise.resolve().then(() => {
    console.log("second")
  })
})

// op
// first
// second
```

- The event loop will **keep draining the microtask queue until it is empty**.

### `process.nextTick()` Behavior

```js
Promise.resolve().then(() => console.log("promise"))

process.nextTick(() => console.log("nextTick"))

// op
// nextTick
// promise
```

- `process.nextTick()` runs **before promise microtasks** because Node prioritizes the `nextTick` queue.
