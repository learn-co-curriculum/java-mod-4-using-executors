# Using Executors

## Learning Goals

- Explain the different executor implementations.
- Create executors.
- Submit tasks to executors.
- Terminate executors.

## `Executors` Class

The `Executors` class in the `java.util.concurrent` package provides factory
methods for instantiating executor implementations. The most common factory
methods are:

- `newFixedThreadPool`
- `newSingleThreadExecutor`
- `newCachedThreadPool`
- `newScheduledThreadPool`

### newFixedThreadPool

This has a fixed number of threads. We can submit as many tasks for execution as
we want. Threads that have finished running tasks are reused to run the queued
tasks. The fixed thread pool executor is the most common one you’ll use.

```java
ExecutorService executor = Executors.newFixedThreadPool(5);
```

### newSingleThreadExecutor

This executor only has a single thread for running rarely submitted or small
tasks. If a lot of tasks are queued at the same time it will consume most of the
memory. It’s important to make sure that the single thread can process the task
quickly and to avoid queuing too many tasks in a short period.

```java
ExecutorService executor = Executors.newSingleThreadExecutor();
```

### newCachedThreadPool

This type of executor provides a growing thread pool. It creates new threads as
needed and uses old threads that have completed their tasks. It can be
configured to terminate threads that are waiting too long to get a task. These
executors are usually used for handling short-lived async tasks.

```java
ExecutorService executor = Executors.newCachedThreadPool();
```

### newScheduledThreadPool

This executor is used for executing tasks that need to be run periodically or
with a delay.

```java
ScheduledExecutorService executor = Executors.newSingleThrean.dScheduledExecutor();
```

We can use the `scheduleAtFixedRate`, `schedule`, and `scheduleWithFixedDelay`
methods on the `executor` instance to create and execute tasks.

## Task Submission and Executor Shutdown

Different types of executors provide different methods for running tasks. We
will be using an executor with a fixed thread pool which has the `submit` method
for enqueueing tasks. The method takes in an instance of a class that implements
the `Runnable` interface.

There are two methods for shutting down an executor:

- `shutdown`
- `shutdownNow`

The `shutdown` method waits for all the enqueued tasks to complete running and
prevents any new tasks from being added to the queue.

The `shutdownNow` method stops all running tasks and returns the tasks in the
queue as a `List`.

Let’s look at an example:

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.stream.IntStream;

class Example {
    private final static int NUMBER_OF_THREADS = 3;
    private final static int NUMBER_OF_TASKS = 6;

    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(NUMBER_OF_THREADS);

        IntStream.rangeClosed(1, NUMBER_OF_TASKS)
                .forEach(i -> {
                    executor.submit(() -> {
                        String task = "Task-" + i;
                        String thread = Thread.currentThread().getName();
                        System.out.printf("%s is executing %s\n", thread, task);
                    });
                });

        executor.shutdown();
    }
}
```

```plaintext
pool-1-thread-3 is executing Task-3
pool-1-thread-2 is executing Task-2
pool-1-thread-1 is executing Task-1
pool-1-thread-2 is executing Task-4
pool-1-thread-2 is executing Task-5
pool-1-thread-3 is executing Task-6
```

Here, we have created an executor with a fixed thread pool of 3 threads and we
enqueue 6 tasks to it. The executor takes the tasks and runs them in a new
thread. Once the tasks are finished running the thread can be reused. Notice
that `thread-2` and `thread-3` are used to run different tasks.

## Conclusion

We have learned how to use executors for automatically handling multiple
threads. This makes it much easier to create multithreaded programs while also
making them more efficient.
