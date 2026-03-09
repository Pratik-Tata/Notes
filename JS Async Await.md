

---

# 1. The Problem `async/await` Solves

JavaScript is **single-threaded**, but it handles async work using:

- **Callbacks**
    
- **Promises**
    
- **async / await** (syntax sugar over Promises)
    

Example with Promises:

```javascript
fetch("/api/data")
  .then(res => res.json())
  .then(data => console.log(data))
```

This quickly becomes messy when many operations depend on each other.

`async/await` lets you write **asynchronous code that looks synchronous.**

---

# 2. What `async` Actually Does

Declaring a function `async` automatically makes it **return a Promise**.

```javascript
async function greet() {
  return "hello";
}
```

Equivalent to:

```javascript
function greet() {
  return Promise.resolve("hello");
}
```

Usage:

```javascript
greet().then(console.log)
```

Output

```
hello
```

Important rule:

**Every async function returns a Promise.**

---

# 3. What `await` Does

`await` pauses execution **until a Promise resolves**.

Example:

```javascript
async function getData() {

  let response = await fetch("/api/user");

  let data = await response.json();

  console.log(data);

}
```

Execution flow:

```
call getData()
 ↓
fetch starts
 ↓
function pauses at await
 ↓
JS event loop continues other work
 ↓
when promise resolves → function resumes
```

Key insight:

**`await` does NOT block the thread.  
It suspends the function.**

---

# 4. Under the Hood (How await Works)

This:

```javascript
let data = await fetch(url)
```

is roughly transformed to:

```javascript
fetch(url).then(data => {
   // resume execution
})
```

So `await` is basically **syntactic sugar over `.then()`**.

---

# 5. Event Loop Interaction

Understanding `await` requires understanding **tasks in the event loop**.

JavaScript queues work in two main places:

### Macrotask Queue

Examples:

- `setTimeout`
    
- `setInterval`
    
- DOM events
    
- network callbacks
    

### Microtask Queue

Higher priority queue.

Examples:

- `Promise.then`
    
- `await`
    
- `queueMicrotask`
    

---

# 6. Event Loop Priority

Each event loop cycle:

```
1. Run synchronous code
2. Run ALL microtasks
3. Run ONE macrotask
4. Repeat
```

Microtasks always run **before macrotasks**.

---

# 7. Example: Microtask vs Macrotask

```javascript
console.log("start");

setTimeout(() => {
  console.log("timeout");
}, 0);

Promise.resolve().then(() => {
  console.log("promise");
});

console.log("end");
```

Output:

```
start
end
promise
timeout
```

Explanation:

```
start        (sync)
end          (sync)

promise      (microtask)
timeout      (macrotask)
```

---

# 8. Where `await` Goes

When `await` resolves, the continuation is placed in the **microtask queue**.

Example:

```javascript
async function test() {
  console.log("A");

  await Promise.resolve();

  console.log("B");
}

console.log("C");

test();

console.log("D");
```

Output:

```
C
A
D
B
```

Explanation:

```
C (sync)
test() runs
A (sync)
await encountered → pause function
D (sync)
B (microtask resume)
```

---

# 9. Can `await` Run Without `async`?

Short answer:

**No (normally).**

Example:

```javascript
function test() {
  await fetch("/api");
}
```

Result:

```
SyntaxError: await is only valid in async functions
```

Because `await` requires the function to return a Promise.

Correct version:

```javascript
async function test() {
  await fetch("/api");
}
```

---

# 10. Exception: Top-Level Await

In **modern ES modules**, you can use `await` at the top level.

Example:

```javascript
const data = await fetch("/api/data");
```

But only works in:

- ES modules
    
- Node modern environments
    
- not older scripts
    

---

# 11. Sequential vs Parallel Await

Common beginner mistake.

### Sequential (slow)

```javascript
async function load() {

  let user = await fetch("/user");

  let posts = await fetch("/posts");

}
```

Total time:

```
user time + posts time
```

---

### Parallel (better)

```javascript
async function load() {

  let userPromise = fetch("/user");
  let postPromise = fetch("/posts");

  let user = await userPromise;
  let posts = await postPromise;

}
```

or

```javascript
const [user, posts] = await Promise.all([
  fetch("/user"),
  fetch("/posts")
]);
```

Total time:

```
max(user, posts)
```

---

# 12. Error Handling

Use `try / catch`.

```javascript
async function load() {

  try {

    let data = await fetch("/api");

  } catch(err) {

    console.log("error", err);

  }

}
```

Equivalent to:

```javascript
fetch("/api").catch(...)
```

---

# 13. Awaiting Non-Promises

`await` works with **any value**.

```javascript
async function test() {

  let x = await 5;

  console.log(x);

}
```

Output:

```
5
```

Internally:

```
await value
→ Promise.resolve(value)
```

---

# 14. Important Performance Insight

`await` **splits execution into two phases**.

Example:

```javascript
await something();
```

means:

```
1. run until await
2. schedule continuation as microtask
3. resume later
```

Too many awaits can create **excessive microtasks**.

---

# 15. Classic Interview Question

Predict output:

```javascript
async function test() {

  console.log("1");

  await Promise.resolve();

  console.log("2");

}

console.log("3");

test();

console.log("4");
```

Answer:

```
3
1
4
2
```

---

# 16. Mental Model

Think of `await` like this:

```
async function

↓ runs

hit await

↓ pause

promise resolves

↓ schedule microtask

↓ resume function
```

---

# 17. When NOT to Use Await

Avoid this pattern:

```javascript
for (let url of urls) {
   await fetch(url)
}
```

Better:

```javascript
await Promise.all(
   urls.map(url => fetch(url))
)
```

Otherwise requests run **sequentially instead of parallel**.

---

# Final One-Line Mental Model

`async/await` = **Promise syntax that pauses the function and resumes it as a microtask when the promise resolves.**

---

# 1. Quick Recap: Event Loop Order

Each event loop tick roughly does:

```
1. Run synchronous code
2. Run ALL microtasks
3. Run ONE macrotask
4. Repeat
```

Priority:

```
Microtasks > Macrotasks
```

So microtasks run **immediately after the current stack finishes**.

---

# 2. How to Schedule a Microtask

There are **three main ways**.

### 1️⃣ Promise.then()

Most common.

```javascript
console.log("start");

Promise.resolve().then(() => {
  console.log("microtask");
});

console.log("end");
```

Output:

```
start
end
microtask
```

Because `.then()` always schedules a **microtask**.

---

### 2️⃣ queueMicrotask()

This API exists specifically for scheduling microtasks.

```javascript
console.log("start");

queueMicrotask(() => {
  console.log("microtask");
});

console.log("end");
```

Output:

```
start
end
microtask
```

This is basically the **explicit microtask scheduler**.

---

### 3️⃣ await

When a promise resolves, `await` resumes as a microtask.

```javascript
async function test() {
  await null;
  console.log("microtask from await");
}

test();
```

Equivalent to:

```
Promise.then(...)
```

---

# 3. How to Schedule a Macrotask

Macrotasks go into the **task queue**.

### 1️⃣ setTimeout

Most common macrotask scheduler.

```javascript
setTimeout(() => {
  console.log("macrotask");
}, 0);
```

Even with `0`, it **does not run immediately**.

It waits until:

```
current code
+ microtasks
finish
```

---

### 2️⃣ setInterval

Also macrotask.

```javascript
setInterval(() => {
  console.log("tick");
}, 1000);
```

Runs repeatedly via macrotask queue.

---

### 3️⃣ DOM Events

Example:

```
click
scroll
keydown
```

These callbacks are scheduled as **macrotasks**.

---

# 4. Micro vs Macro Example

```javascript
console.log("1");

setTimeout(() => console.log("2"));

Promise.resolve().then(() => console.log("3"));

console.log("4");
```

Output:

```
1
4
3
2
```

Explanation:

```
1 (sync)
4 (sync)

3 (microtask)

2 (macrotask)
```

---

# 5. Multiple Microtasks

All microtasks run **before the next macrotask**.

Example:

```javascript
console.log("start");

Promise.resolve().then(() => console.log("micro1"));

queueMicrotask(() => console.log("micro2"));

setTimeout(() => console.log("macro"));

console.log("end");
```

Output:

```
start
end
micro1
micro2
macro
```

---

# 6. Why Microtasks Exist

They allow **post-processing after the current stack but before rendering or other events**.

Used heavily by:

- Promises
    
- MutationObserver
    
- async/await
    
- frameworks
    

Example use case:

```
finish current operation
→ then update state
→ before next UI event
```

---

# 7. Node.js Special Microtask: `process.nextTick()`

Node has an **extra queue**.

Example:

```javascript
process.nextTick(() => {
  console.log("nextTick");
});
```

Priority order in Node:

```
nextTick queue
microtask queue
macrotask queue
```

So `nextTick` runs **before promises**.

---

# 8. Real Example Showing Everything

```javascript
console.log("start");

setTimeout(() => console.log("timeout"), 0);

queueMicrotask(() => console.log("microtask"));

Promise.resolve().then(() => console.log("promise"));

console.log("end");
```

Output:

```
start
end
microtask
promise
timeout
```

---

# 9. Why This Matters in Real Apps

Example: state update batching.

Frameworks often do:

```
update state
queue microtask
batch updates
render once
```

Instead of rendering **100 times**.

---

# 10. Trick Interview Question

Predict output:

```javascript
setTimeout(() => console.log("timeout"));

Promise.resolve().then(() => {
  console.log("promise1");
  return Promise.resolve();
}).then(() => console.log("promise2"));
```

Answer:

```
promise1
promise2
timeout
```

Because `.then()` keeps scheduling **microtasks**, and the event loop clears them **before macrotasks**.

---

# 11. When to Use Each

### Use Microtasks when

You want something to run:

```
after current code
but before next event
```

Example:

```
state updates
promise resolution
cleanup
```

---

### Use Macrotasks when

You want to:

```
delay execution
yield control to browser
schedule future work
```

Example:

```
timers
polling
UI events
```

---

# Clean Mental Model

```
call stack empty
       ↓
run microtasks (all)
       ↓
run 1 macrotask
       ↓
repeat
```

---

