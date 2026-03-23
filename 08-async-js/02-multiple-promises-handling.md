## Promise Chaining

- Promises allow chaining operations sequentially.

```js
Promise.resolve(1)  
  .then(x => x + 1)  
  .then(x => x + 1)  
  .then(result => console.log(result))

// o/p:
// 3
```

- Each `.then()` returns **a new promise**. 
- Conceptually: `Promise → then → new Promise → then → new Promise` This allows composition of async operations.

## Promise Flattening

- If a `.then()` returns another promise, the chain **waits for it**.

```js
Promise.resolve(1)  
  .then(x => {  
    return new Promise(resolve => {  
      setTimeout(() => resolve(x + 1), 1000)  
    })  
  })  
  .then(result => console.log(result))

// Output after 1 second:
// 2
```

- This behavior is called **promise flattening**.

## Promise Combinators

- Promises provide utilities for coordinating multiple async operations.
### Promise.all

- Runs operations in parallel.

```js
Promise.all([  
  fetch("/user"),  
  fetch("/posts")  
]).then(([user, posts]) => {  
  console.log(user, posts)  
}).catch(err) => {
  console.log(err)
}
```

- Behavior: `all promises must succeed, otherwise reject`
- Returns a promise that resolves to an **array of fulfillment values** in the same order as the input. It rejects immediately if _any_ promise rejects.
### Promise.race

- Resolves when the **first promise settles**.

```js
Promise.race([  
  fetch("/api"),  
  timeoutPromise  
])
```

- Behavior: `first promise to resolve is returned, others ignored`
- Returns a promise that settles (resolves or rejects) as soon as the **first promise** in the array settles.
### Promise.allSettled

- Waits for all promises regardless of success.

```js
Promise.allSettled([  
  p1,  
  p2,  
  p3  
])
```

- Behavior: `all promises must complete, regardless of resolve or reject`
- Returns a promise that resolves to an **array of objects** describing the outcome of each promise (`{status: 'fulfilled', value: ...}` or `{status: 'rejected', reason: ..}`).
### Promise.any

- Resolves when **first promise fulfills**. Rejects only if all fail.
- Returns a promise that fulfills as soon as the **first promise fulfills**. If all reject, it rejects with an `AggregateError`.

