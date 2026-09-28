# Introduction to Real-Time Operating Systems

*Lecture 12 · Real-Time Operating Systems*

## What is a Real-Time Operating System?

A **real-time operating system (RTOS)** is an operating system designed to run applications with timing constraints. Its main goal is **predictability**: operations must complete within known, bounded times so that deadlines can be guaranteed.

---

## Why Use an RTOS?

Simple embedded software can run as a single loop (a "super-loop") that polls inputs and calls functions in turn. As systems grow, this approach becomes hard to maintain and analyse. An RTOS provides:

- **Concurrency** — split the application into independent tasks
- **Priority-based scheduling** — urgent work pre-empts less urgent work
- **Timing services** — periodic activation, delays, and timeouts
- **Communication and synchronisation** — safe data exchange between tasks
- **Portability** — a standard API across different hardware platforms

---

## RTOS vs General-Purpose OS

| Aspect | RTOS | General-Purpose OS |
|--------|------|--------------------|
| **Primary goal** | Predictability, meeting deadlines | Throughput, fairness, average performance |
| **Scheduling** | Fixed-priority pre-emptive (typically) | Time-sharing, fairness-based |
| **Latency** | Designed to support bounded timing under stated conditions | Usually optimised for average responsiveness |
| **Footprint** | Often configurable for a constrained target | Typically supports a broader collection of services |
| **Examples** | FreeRTOS, Zephyr, VxWorks, QNX, RTEMS | Linux, Windows, macOS |

---

## Tasks

A **task** (or thread) is an independent unit of execution with its own stack and priority. During its lifetime, a task moves between states:

- **Ready** — able to run, waiting for the processor
- **Running** — currently executing
- **Blocked** — waiting for an event, resource, or time delay
- **Suspended** — explicitly removed from scheduling

The **scheduler** decides which ready task runs next. A **context switch** saves the state of the current task and restores the state of the next one.

Preemption and activation are separate choices. A time-triggered cyclic executive uses a schedule established offline. An event-triggered application activates work in response to events and may use either preemptive tasks or handlers that run to completion. See [embedded software design](embedded-software.md#choosing-an-execution-structure) for the trade-offs.

---

## Kernel Services

| Service | Purpose |
|---------|---------|
| **Semaphores** | Signal events and count available resources |
| **Mutexes** | Mutual exclusion for shared resources, often with priority inheritance |
| **Message queues** | Pass data between tasks and from interrupts to tasks |
| **Timers** | Periodic activation, delays, and timeouts |
| **Interrupt handling** | Respond to hardware events, deferring work to tasks |

!!! warning
    Sharing resources between tasks of different priorities can cause **priority inversion**, where a high-priority task is delayed by lower-priority ones. This is covered in [Multicore and Resource Sharing](../multicore-resource-sharing/introduction.md).

---

## Next Steps

- Study [FreeRTOS task states, queues, mutexes and memory](freertos.md) as a concrete kernel example
- Learn how tasks are scheduled to meet their deadlines in [Real-Time Scheduling](../real-time-scheduling/introduction.md)
