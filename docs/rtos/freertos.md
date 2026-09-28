# FreeRTOS: Tasks, Communication and Memory

*Lecture 12 · Real-Time Operating Systems*

FreeRTOS provides a concrete example of the mechanisms behind the [RTOS overview](introduction.md). This chapter considers a single-core configuration with preemptive scheduling. Kernel configuration and the processor port determine the available behaviour.

## Task execution and state

A task has a function, priority, stack and task control block. A context switch preserves enough processor state for the task to resume. A task function normally loops and blocks when it has no work; it must not simply return.

| State | Meaning | Typical transition |
|---|---|---|
| Ready | Able to execute, waiting for the processor | Selected by the scheduler → Running |
| Running | Currently using the processor | Wait for input → Blocked |
| Blocked | Waiting for an event or timeout | Event or timeout → Ready |
| Suspended | Explicitly excluded from scheduling | Explicit resume → Ready |

A newly ready high-priority task can pre-empt a lower-priority task. A task unblocked by a timeout does not necessarily execute immediately: it must still win the scheduling decision. See the official [task-state definitions](https://www.freertos.org/Documentation/02-Kernel/02-Kernel-features/01-Tasks-and-co-routines/02-Task-states).

## Choose the communication mechanism

| Need | Mechanism | What to account for |
|---|---|---|
| Pass data items | Queue | Item size, capacity, copies and timeouts |
| Signal an event | Binary semaphore or task notification | Whether repeated events may be merged |
| Count available units | Counting semaphore | Initial and maximum count |
| Protect a shared object | Mutex | Ownership and priority inheritance |

Queue items have a fixed size and are copied into queue storage. If the item is a pointer, only the pointer is copied: the application must manage the pointed-to object's lifetime and ownership. Blocking on an empty queue allows other ready tasks to execute; polling continuously consumes processor time.

FreeRTOS mutexes provide a basic priority inheritance mechanism; binary semaphores do not. Mutexes are for task mutual exclusion and must not be used from an interrupt handler. The kernel's simplified inheritance behaviour also needs care when a task holds multiple mutexes. See the [mutex documentation](https://freertos.org/Real-time-embedded-RTOS-mutexes.html).

## Interrupts and deferred work

An interrupt handler should acknowledge the event, capture the necessary information and wake a task to perform longer processing. Use the permitted `FromISR` APIs, follow the port's interrupt-priority restrictions, and request a reschedule when required. An interrupt handler cannot block waiting for a queue or mutex.

Define what happens when events arrive faster than the task can process them. A full queue, lost notification or overwritten sample must have an intentional response rather than silently invalidating the system model.

## Memory is part of the design

Budget for each task's stack, kernel objects, queued data and application buffers. Static allocation lets the application provide object storage in advance; dynamic allocation requests storage from a heap. FreeRTOS supports both, depending on configuration. See [static and dynamic allocation](https://www.freertos.org/Documentation/02-Kernel/02-Kernel-features/09-Memory-management/03-Static-vs-Dynamic-memory-allocation).

The lecture discusses these heap implementations:

| Heap | Main distinction |
|---|---|
| `heap_1` | Allocates without freeing |
| `heap_2` | Frees blocks without coalescing adjacent free blocks |
| `heap_3` | Wraps the C library allocator |
| `heap_4` | Coalesces adjacent free blocks |
| `heap_5` | Extends the coalescing approach to multiple memory regions |

Coalescing helps with fragmentation but does not itself prove constant allocation time or guarantee success. For timing-critical paths, establish allocation bounds or allocate required objects before normal execution.

## From kernel settings to a timing argument

Record priorities, preemption configuration, tick rate, interrupt behaviour, stack sizes and queue bounds alongside the task parameters. Using an RTOS does not by itself guarantee deadlines: [schedulability analysis](../real-time-scheduling/introduction.md) must match the configured application and include relevant overheads.

??? question "Check your understanding"
    A task blocks on a queue for at most ten ticks. Does that guarantee it processes a message within ten ticks?

    No. The timeout bounds the queue wait; the task can then wait in Ready state and still needs execution time to handle a message. A timeout may also occur without receiving any message.
