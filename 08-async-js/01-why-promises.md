## The Callbacks

- Before promises, JavaScript async code relied almost entirely on **callbacks**.

```js
fs.readFile("data.txt", (err, data) => {
  if (err) {
    console.error(err)
  } else {
    console.log(data)
  }
})
```

- Imagine multiple dependent operations:

```js
fs.readFile("file1", (err, data1) => {
  fs.readFile("file2", (err, data2) => {
    fs.readFile("file3", (err, data3) => {
      console.log("done")
    })
  })
})
```

- This triangle is called `The Pryamid of Doom` and the callbacks lead to a phenomenon called `The Callback Hell`.

- Problems with callbacks:
	- deeply nested code
	- hard error propagation
	- inversion of control
	- difficult composition

## Promises

- A promise represents the **eventual result of an asynchronous operation**.
- Promise = placeholder for a future value

```js
const promise = fetch("/api/data")
console.log(promise)

// o/p
// Promise { <pending> }
```
- `fetch()` immediately returns a promise even though the network request has not finished.

## Promise States

- Every promise has three possible states - `pending, fulfilled, rejected`

```txt
pending
   │
   ├── fulfilled (resolved successfully)
   │
   └── rejected (error occurred)
```

- Once a promise is fulfilled or rejected - **its state cannot change** This property is called **immutability**.

## Creating a Promise

- A promise is created using the `Promise` constructor - `new Promise(executor)`.
- The constructor receives an **executor function** which determine the final state.
- This function gets two arguments: `resolve reject`.

```js
const promise = new Promise((resolve, reject) => {
  const success = true

  if (success) {
    resolve("operation successful")
  } else {
    reject("operation failed")
  }
})
```

## Consuming a Promise

- You interact with a promise using `.then()` and `.catch()` methods.

```js
promise
  .then(result => {
    console.log(result)
  })
  .catch(error => {
    console.error(error)
  })
```

- `.then()` handles fulfillment.
- `.catch()` handles rejection.
## Promise Resolution Is Asynchronous

- Even if a promise resolves immediately, the `.then()` handler runs asynchronously.
- Because promise handlers are placed into the **microtask queue**.

```js
console.log("start")

Promise.resolve().then(() => {
  console.log("promise")
})

console.log("end")
```

```js
// o/p
start
end
promise
```

## Promise Microtask Scheduling

- When a promise resolves:

```txt
resolve() called  
↓  
.then() callback scheduled  
↓  
microtask queue  
↓  
event loop executes it
```

```txt
call stack empty  
↓  
microtask queue checked  
↓  
promise callbacks executed
```

- That’s why promises run before timers.

## Error Propagation

- Errors propagate through the chain automatically.

```js
Promise.resolve()  
  .then(() => {  
    throw new Error("failure")  
  })  
  .catch(err => {  
    console.log("caught:", err.message)  
  })

// Output:
// caught: failure
```

- The promise becomes **rejected**, and the next `.catch()` handles it.

## How Promises Work Internally (Conceptually)

- A promise internally maintains - `state, result, list of handlers`.

- When you call `.then()` - `handler stored`
- When promise resolves - `handlers scheduled in microtask queue`

- Then the event loop executes them.

## Visualizing Promise Execution

```js
Promise.resolve(1)  
  .then(x => x + 1)  
  .then(x => console.log(x))
```

- Flow:
```txt
Promise resolved  
↓  
.then handler queued (microtask)  
↓  
event loop executes handler  
↓  
new promise resolved  
↓  
next handler queued  
↓  
event loop executes handler
```

- Promise handlers: `never run synchronously, always run in microtasks`. This ensures predictable execution order.

- You can think of promises as:
```txt
Promise  
  =  
Future value  
  +  
Microtask scheduling  
  +  
Chaining mechanism
```

