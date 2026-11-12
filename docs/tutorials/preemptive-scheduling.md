# Preemptive vs Non-preemptive Scheduling

This interactive tutorial runs one task set under two fixed-priority schedulers and draws both timing diagrams on a shared time axis. Both schedulers always pick the highest-priority ready task. They differ only in **when** they are allowed to pick: a preemptive scheduler decides again whenever a task is released, while a non-preemptive scheduler decides only when the processor becomes free.

!!! info "What you'll learn"
    - How a newly released high-priority task preempts a running lower-priority one
    - Why, without preemption, a high-priority task can be **blocked** by a lower-priority task that is already running
    - That blocking under non-preemptive scheduling comes from at most one lower-priority task
    - Why preemption is not free: it adds context switches and can push a low-priority task past its deadline

---

## How to Use

**Step through time:** use **Step ▶** and **◀ Step**, drag the time slider, or press **Play**. The left and right arrow keys also step. At each time unit, the panel below the charts explains each scheduler's decision.

**Read the charts:** a solid bar means the task is running, a pale bar means it is ready and waiting, and a red hatched bar means it is waiting behind a lower-priority task. An up arrow marks a release and a down arrow a deadline; the deadline arrow turns red when it is missed.

**Show the schedule after t** draws the whole schedule at once. The **Non-preemptive** and **Preemptive** checkboxes hide either chart.

**Edit the task set:** change each task's release time, execution time, priority and relative deadline (leave it empty for no deadline), or add up to six tasks. The switch below the table sets whether 1 or the larger number is the highest priority. Equal priorities are served in release order and do not preempt each other.

---

## Interactive Tutorial

<a href="../embeds/preemptive-scheduling.html" target="_blank" rel="noopener" class="md-button">&#x2197; Open in new window</a>

<iframe src="../embeds/preemptive-scheduling.html" title="Interactive preemptive vs non-preemptive priority scheduling" class="tutorial-frame" width="100%" height="1250" loading="lazy"></iframe>

---

## Key Observations

!!! example "Things to try"
    1. **Urgent arrival** (the default). Without preemption, T3 starts at t = 0 and runs for 6 units, so T2 and T1 are blocked and both miss their deadlines. With preemption, T2 preempts T3 at t = 2 and T1 preempts T2 at t = 3: every deadline is met, at the cost of two preemptions.
    2. **One tick too late.** T2 is released one unit after an 8-unit low-priority task starts. Its response time is 1 with preemption and 8 without, so its deadline of 2 is missed by a wide margin.
    3. **All released together.** The two schedules are identical. When nothing is released while a lower-priority task runs, there is nothing to preempt.
    4. **Low priority pushed back.** Preemption does not meet every deadline. The high-priority tasks now meet theirs, but T1 is pushed back until t = 13 and misses its deadline of 12, which it met without preemption.
    5. In **Urgent arrival**, shorten T3's execution time. The blocking that T1 suffers without preemption shrinks with it: non-preemptive blocking is bounded by the longest remaining lower-priority execution.

---

## Related Material

- [Introduction to Real-Time Operating Systems](../rtos/introduction.md)
- [FreeRTOS: Tasks, Communication and Memory](../rtos/freertos.md)
- [Embedded Software Design](../rtos/embedded-software.md#choosing-an-execution-structure)
- [Fixed-Priority Scheduling](../real-time-scheduling/fixed-priority.md)
