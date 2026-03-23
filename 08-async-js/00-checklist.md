# 10. Generators for Async Flow

Generators were once used for async control before `async/await`.

Topics:

- generator execution
    
- yielding promises
    
- generator runners
    

Example concept:

run(generatorFunction)

Libraries like **co** used this.

---

# 11. Async Iteration

Used for streaming async data.

Topics:

- async generators
    
- `Symbol.asyncIterator`
    
- `for await...of`
    

Example:

for await (const chunk of stream)

Used in:

- data streams
    
- file processing
    
- network pipelines
    

---

# 12. Cancellation & Timeouts

Real async systems need cancellation.

Concepts:

- `AbortController`
    
- request cancellation
    
- timeout wrappers
    

Example:

fetch(url, { signal })

---

# 13. Concurrency Control

Advanced async systems require controlling load.

Topics:

- task queues
    
- worker pools
    
- rate limiting
    
- retries with backoff
    

Example utility:

limitConcurrency(tasks, max)

---

# 14. Event Driven Systems

Many async systems are event-based.

Topics:

- event emitters
    
- publish/subscribe
    
- reactive streams
    

Node.js relies heavily on this model.

---

# 15. Async Debugging & Performance

Mastering async also means diagnosing problems.

Topics:

- race conditions
    
- memory leaks in async flows
    
- unhandled promise rejections
    
- async stack traces
    

---

# The Key Insight

Async JavaScript is built from only a few primitives:

Event Loop  
+ Queues  
+ Promises  
+ Generators

Everything else (including `async/await`) is layered on top.

---

# The Most Important Skill for Mastering Async JS

You should be able to **predict the output order** of complex async code.

Example challenge:

console.log("A")  
  
setTimeout(() => console.log("B"))  
  
Promise.resolve().then(() => console.log("C"))  
  
console.log("D")

A developer who deeply understands async JS can reason about this **without running the code**.