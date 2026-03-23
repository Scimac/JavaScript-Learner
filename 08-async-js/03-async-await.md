## What's `async` / `await`

- `async` / `await` is **syntax sugar on top of promises**. It does not introduce a new concurrency system.

```js
async function getData() {
  const data = await fetch("/api")
  return data
}
```

- Internally, this function still uses **promises and microtasks**.

### What `async` does

- Important rule:  `An async function ALWAYS returns a Promise.`

```js
async function test() {  
  return 42  
}  
  
console.log(test())

// Output:
// Promise { 42 }
```

- Even though we returned a value directly, JavaScript wraps it in a promise.

### What `await` does

- `await` pauses the execution of the async function **until the promise resolves**, but it does **not block the thread**.

```js
async function example() {
  console.log("A")

  const result = await Promise.resolve("B")

  console.log(result)
}

example()
console.log("C")

// o/p
// A
// C
// B
```

- Because `await` splits the function into two parts.
```js
function example_promise_version() {
  console.log("A");
  return Promise.resolve("B").then(result => {
    console.log(result);
  });
}

example_promise_version();
console.log("C");
```

- Conceptually:
```txt
before await  
↓  
pause  
↓  
resume later as microtask
```

- So `await` simply becomes **promise chaining**.

```js
async function run() {  
  console.log("1")  
  
  await Promise.resolve()  
  
  console.log("2")  
}  
  
run()  
console.log("3")

// Execution order:
// 1  
// 3  
// 2
```

- Explanation:
	1. `run()` starts executing
	2. `await` schedules continuation as a **microtask**
	3. main code continues
	4. microtask runs later

## Awaiting Non-Promises

- `await` works even with non-promises.

```js
async function test() {  
  const value = await 10  
  console.log(value)  
}  
  
test()
```

- Internally this becomes: `Promise.resolve(10).then(...)`. So `await` always converts its value into a promise.

## Error Handling

- Promises propagate errors through rejection. `async/await` allows using `try/catch`.

```js
async function load() {  
  try {  
    const data = await fetch("/api")  
    console.log(data)  
  } catch (err) {  
    console.error("error:", err)  
  }  
}
```

- Equivalent promise version:
```js
fetch("/api")  
  .then(data => console.log(data))  
  .catch(err => console.error(err))
```

## Sequential vs Parallel Await

### Sequential Execution

```js
async function load() {  
  const a = await fetch("/a")  
  const b = await fetch("/b")  
}
```

- Execution:

```txt
request A  
wait  
request B  
wait

Total time = A + B
```

### Parallel Execution

```js
async function load() {  
  const pa = fetch("/a")  
  const pb = fetch("/b")  
  
  const a = await pa  
  const b = await pb  
}
```

- Execution:

```txt
request A  
request B  
wait for both

Total time = max(A,B)
```

## Await in Loops

- A common mistake. This runs sequentially.

```js
for (const id of ids) {  
  await fetch(`/user/${id}`)  
}
```

```txt
Req 1
wait
Req 2
wait

Req 1 resolves
Req3
wait

Req 2 resolves
wait
Req 3 resolves
```

- Better approach :

```js
await Promise.all(  
  ids.map(id => fetch(`/user/${id}`))  
)
```

```txt
Req 1, Req 2, Req 3 Triggered
waits

All resolved 
```

- Now all requests run in parallel.

## Async Iteration

- Async iteration allows consuming **streams of asynchronous data**.

```js
for await (const chunk of stream) {  // await in the for statement, nt in loop
  console.log(chunk)  
}
```

- This works with **async iterators**, often used in:
	- data streams
	- file processing
	- APIs returning paginated data

## Summary: The Async Function State Machine

- Internally an async function behaves like a **state machine**.
- Conceptually:

```txt
start  
↓  
execute until await  
↓  
pause  
↓  
schedule continuation  
↓  
resume later
```

- Each `await` creates a new continuation step. 
- When an awaited promise resolves:

```txt
promise resolved  
↓  
continuation scheduled  
↓  
microtask queue  
↓  
event loop runs continuation
```

- So async functions are tightly coupled to **microtask scheduling**.

## Why `async/await` Improves Readability


```js
// Promise chain:

fetch("/user")  
  .then(res => res.json())  
  .then(user => fetch(`/posts/${user.id}`))  
  .then(res => res.json())  
  .then(posts => console.log(posts))
```

```js
// Async version:

async function load() {  
  const user = await fetch("/user").then(r => r.json())  
  const posts = await fetch(`/posts/${user.id}`).then(r => r.json())  
  console.log(posts)  
}
```

- The async version reads **top-to-bottom like synchronous code**. But behaves like **asynchronous code**.




