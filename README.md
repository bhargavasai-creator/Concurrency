# ExecutorExample: Java Executor Framework Demo

A small Java program that shows how to turn a `Runnable` into a `Callable` with `Executors.callable(...)` and how to submit a task to a single-thread `ExecutorService`. It is a compact starting point for learning Java's `java.util.concurrent` executor API.

<!-- TODO: if this is coursework (package name suggests BU MET CS 622), add a line saying so. -->

## What It Demonstrates

- Printing the current thread name (`main`)
- Creating a `Runnable` with a lambda
- Converting that `Runnable` to a `Callable` with `Executors.callable(runnable)`
- Inspecting the adapter class the JDK creates for the conversion
- Creating a single-thread executor with `Executors.newSingleThreadExecutor()`
- Submitting the task with `submit(...)` and shutting the executor down with `shutdown()`

## Project Structure

```
src
└── edu/bu/met622/threadtest/trylock
    └── ExecutorExample.java
```

<!-- TODO: the package name "trylock" suggests sibling examples (for example ReentrantLock.tryLock). List them here if they are in the same repo. -->

## How It Works

| Step | Code | What happens |
|---|---|---|
| 1 | `Thread.currentThread().getName()` | Prints the name of the thread running `main` |
| 2 | `Runnable runnable = () -> { ... }` | Defines a task. The body is empty, so it does nothing visible |
| 3 | `Executors.callable(runnable)` | Wraps the `Runnable` in a `Callable` that returns `null` when it finishes |
| 4 | `getClass().getSimpleName()` / `getTypeName()` | Prints the wrapper class: `RunnableAdapter` (`java.util.concurrent.Executors$RunnableAdapter`) |
| 5 | `Executors.newSingleThreadExecutor()` | Creates an executor with one worker thread |
| 6 | `executorService.submit(runnable)` | Queues the task on the worker thread and returns a `Future<?>` (not used here) |
| 7 | `executorService.shutdown()` | Stops accepting new tasks and lets queued ones finish |

## Expected Output

Run on OpenJDK 21:

```
Inside : main
---Callable output:RunnableAdapter---Callable output2:java.util.concurrent.Executors$RunnableAdapter
```

The second line has no line break between the two callable messages because the code uses `System.out.print` rather than `println`.

## Getting Started

### Prerequisites

- JDK 11 or newer

### Compile and run

From the `src` folder:

```bash
javac -d ../out edu/bu/met622/threadtest/trylock/ExecutorExample.java
java -cp ../out edu.bu.met622.threadtest.trylock.ExecutorExample
```

Or run the file directly (JDK 11+):

```bash
java edu/bu/met622/threadtest/trylock/ExecutorExample.java
```

## Notes and Possible Improvements

- **`submit` does return a `Future`.** The comment "Executor (does not return future object)" in the code is not accurate: `submit(runnable)` returns a `Future<?>`. Use `execute(runnable)` if you do not want a `Future`.
- **Raw type warning.** `Callable callable` is a raw type. Declare it as `Callable<Object>` to remove the unchecked warning.
- **The worker thread prints nothing.** The `Runnable` body is empty and its `println` is commented out. Uncomment it to see the worker thread's name, typically `pool-1-thread-1`.
- **Always shut executors down.** `shutdown()` is called here; without it the JVM may keep running because the worker thread stays alive.

### Try it: get a result back

A `Callable` can return a value that you read through the `Future`:

```java
ExecutorService pool = Executors.newSingleThreadExecutor();
Future<Integer> answer = pool.submit(() -> 21 * 2);  // Callable<Integer>
System.out.println(answer.get());                     // prints 42
pool.shutdown();
```

`main` needs `throws Exception` (or a try/catch) because `Future.get()` can throw `InterruptedException` and `ExecutionException`.

## Key Concepts

- **`Runnable`**: a task that returns nothing and cannot throw checked exceptions.
- **`Callable<V>`**: a task that returns a value of type `V` and may throw checked exceptions.
- **`ExecutorService`**: manages a pool of threads and runs submitted tasks, so you do not create threads by hand.
- **`Future<V>`**: a handle to a task's result or failure.
- **`shutdown()` vs `shutdownNow()`**: `shutdown()` lets queued tasks finish; `shutdownNow()` tries to stop them.

## Author

**Bhargav Sai Gundapaneni**
GitHub: [bhargavasai-creator](https://github.com/bhargavasai-creator)

## License

<!-- TODO: choose a license (MIT is a common default) and add a LICENSE file. -->
