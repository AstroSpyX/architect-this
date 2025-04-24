# Processes, Threads, Synchronization, Concurrency Fundamentals

## Process vs Thread

- **Process**: An independent unit of execution (i.e., a program) with its own isolated memory space.
- **Thread**: A lightweight unit of execution within a process that shares memory and resources with other threads in the same process. A process can be thought of as a container for threads.

### Key Differences

- **Memory**: A process has a private memory block not accessible to other processes. Threads share memory space within the same process.
- **Communication**: Inter-process communication (IPC) is more complex and slower compared to inter-thread communication.
- **Creation**: Creating a process involves higher overhead than creating a thread.
- **Context Switching**: Switching between processes is heavier — CPU state must be saved, caches may be flushed, and memory structures are swapped. Thread context switching is lighter due to shared memory.

## Synchronization Primitives

### Mutex

- A **mutex** (mutual exclusion) is a locking mechanism that ensures only one thread can access a critical section of code or a resource at a time.

#### Example: Mutex in Python

```python
import threading

lock = threading.Lock()

def access_shared_resource():
    with lock:
        # Only one thread at a time can enter this block
        pass
```

### Semaphore

- A **semaphore** is a synchronization primitive represented by a non-negative integer counter that regulates access to shared resources by multiple threads.
- A **binary semaphore** is a semaphore that can take only two values — 0 and 1 — and is used to allow or deny access to a single resource. It is similar to a mutex but does not enforce thread ownership.
- A **counting semaphore** is a general form of a semaphore used to manage access to a pool of identical resources, allowing a fixed number of threads to enter a critical section concurrently.

#### Example: Semaphore in Python

```python
import threading

semaphore = threading.Semaphore(3)

def access_shared_resource():
    semaphore.acquire()  # Decreases the internal counter by 1
    try:
        # Access shared resource
        pass
    finally:
        semaphore.release()  # Increases the internal counter by 1
```

## Deadlocks

- A **deadlock** is a state in which two or more threads are blocked forever, each waiting on the other to release a resource
#### Example: deadlock

```python
import threading

lock_a = threading.Lock()
lock_b = threading.Lock()

def run_compute_1():
  with lock_a:
    # execution code
    with lock_b:
      pass

def run_compute_2():
  with lock_b:
    # execution code
    with lock_a:
      pass
```

### Conditions Required for Deadlock (Coffman's Conditions)

Deadlock can occur if all of the following four conditions hold simultaneously:

1. **Mutual Exclusion**: At least one resource must be held in a non-shareable mode — only one thread can use it at a time.
2. **Hold and Wait**: A thread holding at least one resource is waiting to acquire additional resources held by other threads.
3. **No Preemption**: Resources cannot be forcibly taken from threads; they must be released voluntarily.
4. **Circular Wait**: A set of threads are waiting for each other in a circular chain (e.g., T1 waits for T2, T2 waits for T3, ..., Tn waits for T1).

### Strategies to Prevent Deadlock

- **Resource hierarchy / numbering**: Enforce a global order in which resources must be acquired. For example, always acquire `lock_a` before `lock_b` to avoid circular wait.
- **Timeouts**: Use timeouts on lock acquisition to detect and break potential deadlocks.
- **Try-lock pattern**: Use `try_lock()` or `acquire(blocking=False)` with a fallback path.
- **Deadlock detection**: Actively monitor thread/resource state and kill or restart affected threads when deadlock is detected.


#### Example: Try-lock pattern in Python

```python
import threading
import time

lock_a = threading.Lock()
lock_b = threading.Lock()

def try_acquire_locks():
    acquired_a = lock_a.acquire(blocking=False)
    if not acquired_a:
        print("Could not acquire lock_a, aborting.")
        return

    try:
        acquired_b = lock_b.acquire(blocking=False)
        if not acquired_b:
            print("Could not acquire lock_b, releasing lock_a and aborting.")
            return
        try:
            # Both locks acquired successfully, safe to proceed
            print("Both locks acquired, performing task.")
        finally:
            lock_b.release()
    finally:
        lock_a.release()

# Simulate usage
thread1 = threading.Thread(target=try_acquire_locks)
thread2 = threading.Thread(target=try_acquire_locks)

thread1.start()
thread2.start()

thread1.join()
thread2.join()
```

**Explanation**:  
- The function tries to acquire `lock_a` and `lock_b` without blocking.
- If it can't acquire either lock, it backs off immediately — preventing deadlock by avoiding circular waits.
- This pattern is useful when you can afford to fail gracefully or retry later.


