# Starvation under Fixed-Priority Scheduling

This interactive tutorial shows how **fixed-priority scheduling (FPS)** can starve a low-priority task, so that it waits for the processor for a long time or never runs at all. You can compare FPS with two dynamic alternatives on the same tasks: FPS with **ageing**, and **EDF**.

!!! info "What you'll learn"
    - What starvation is, and how it differs from a single missed deadline
    - When response-time analysis predicts starvation: higher-priority tasks that need the whole processor
    - How a burst of high-priority arrivals starves a task in a system that is normally schedulable
    - Why dynamic priorities (ageing, EDF) suffer less from starvation, and what they cost in return

---

## Background

The FPS lecture describes the problem this way:

> Starvation happens when a process with a lower priority is consistently denied access to resources (like CPU time) because higher-priority processes keep arriving and getting scheduled. Dynamic priority scheduling suffers less from starvation.

Under FPS, a ready job runs only when no higher-priority job is ready. The [response-time analysis](../real-time-scheduling/fixed-priority.md#exact-response-time-analysis) of task $\tau_i$ makes this precise:

$$R_i=C_i+\sum_{j\in hp(i)}\left\lceil\frac{R_i}{T_j}\right\rceil C_j.$$

Over a long window of length $t$, the higher-priority tasks need about $t\sum_{j\in hp(i)}U_j$ units of processor time. If

$$\sum_{j\in hp(i)}\frac{C_j}{T_j}\ge1,$$

the right-hand side grows at least as fast as $R_i$, the iteration never converges, and no response time can be guaranteed for $\tau_i$. It can starve completely, as in the first scenario below. Even when the higher-priority utilisation is a little below 1, $\tau_i$ receives only what is left over. If its own demand is larger, its backlog grows without limit.

Response-time analysis also assumes that each task $\tau_j$ is released no more often than once every $T_j$. A burst that breaks this assumption, such as an interrupt storm, can starve lower-priority tasks for the length of the burst, even though the analysis said the system was schedulable.

FPS starvation is **predictable**: it hits the lowest-priority tasks, and higher-priority tasks are unaffected. This is the other side of the [EDF domino effect](edf-domino.md), where an overload spreads across every task.

### Dynamic alternatives

- **Ageing** gradually raises the priority of a job that has waited a long time. In this tutorial, a job gains one priority level for every $A$ time units since its release, until it outranks every fresh job. Ageing is common in general-purpose operating systems. Priorities then change at run time, so the fixed-priority response-time analysis above no longer applies.
- **EDF** ranks jobs by absolute deadline. A waiting job cannot starve indefinitely: any job released after its deadline $d$ has a later deadline, so only a finite amount of work can run ahead of it. The cost is that, under overload, misses spread across all tasks.

---

## How to Use

**Scenario** loads one of three task sets:

- **Higher priorities take everything**: $\tau_1$ and $\tau_2$ use exactly 100% of the processor
- **Burst of arrivals**: normally $U=0.875$, but $\tau_1$ arrives every 2 units instead of every 8 between $t=12$ and $t=36$
- **Slight overload**: $U=1.15$, with $\tau_1$ and $\tau_2$ using 90%

**Scheduler** switches between FPS (priority column, 1 = highest), FPS + ageing and EDF. The ageing step $A$ is set below the task table.

**A job that misses its deadline** either *keeps running* until it finishes, or *is aborted* at its deadline.

**The chart** shows one row per task. Hatched bars under the timeline show a task that has a ready job but is not running: in a starved task they stretch across the chart. Red hatching marks execution after the job's deadline, and dashed red circles mark missed deadlines. The shaded band in a row marks a burst window.

**Play**, **Step** and the slider replay the schedule with an explanation of each decision. Click the chart to jump to a time.

**What happened to each task** lists, for each task, the **longest wait** (the longest stretch with a ready job but no processor) and the **CPU received** compared with the CPU its released jobs needed.

**Edit the task set** to change any parameter. The *Burst* controls make any task arrive more often during a chosen window.

---

## Interactive Tutorial

<a href="../embeds/fps-starvation.html" target="_blank" rel="noopener" class="md-button">&#x2197; Open in new window</a>

<iframe src="../embeds/fps-starvation.html" title="Interactive fixed-priority starvation simulator" class="tutorial-frame" width="100%" height="1920" loading="lazy"></iframe>

---

## Key Observations

!!! example "Things to try"
    1. **Complete starvation.** In the first scenario under FPS, $\tau_3$ never runs: it receives 0 of the 12 units it needs and waits for all 60 time units shown. $\tau_1$ and $\tau_2$ meet every deadline. Check the condition above: $2/4+4/8=1$.
    2. **Ageing and EDF.** Switch to FPS + ageing or to EDF. $\tau_3$ now receives 10 of its 12 units and never waits more than 14 units, but $\tau_1$ and $\tau_2$ start missing deadlines. Overall, both give 19 misses against 5 for FPS. Starvation has been traded for misses spread over every task.
    3. **Burst.** In the burst scenario under FPS, every deadline is met before $t=12$. During the burst, $\tau_3$ waits 26 units in a row, while $\tau_1$ and $\tau_2$ are unaffected. The backlog takes a long time to clear at $U=0.875$: set *Time shown* to 120 and you will see $\tau_3$ keep missing deadlines until $t=72$, long after the burst has ended. Untick *Burst* to confirm that the normal task set is schedulable.
    4. **Burst under EDF.** The same burst under EDF causes 13 misses across all three tasks, but the longest wait falls to 8 units. Which outcome is better depends on which task matters most.
    5. **Tune ageing.** In the burst scenario, change the ageing step from 4 to 1 and then to 20. With a step of 1, $\tau_3$ never waits more than 8 units. With a step of 20, its longest wait grows to 17 units and it misses as many deadlines as under plain FPS (6): the slower the ageing, the closer it is to FPS.
    6. **Abort late jobs.** With *is aborted* in the slight-overload scenario, FPS still gives $\tau_3$ only 6 of the 15 units it needs, and its first four jobs are all aborted. Aborting limits lateness, but it does not give a starved task more processor time.
    7. **Fix it by design.** In the slight-overload scenario, reduce $\tau_1$'s C from 2 to 1. Total utilisation drops to 0.90 and $\tau_3$ meets every deadline under FPS. The usual cure for starvation in a hard real-time system is a schedulable, analysed task set with enforced minimum inter-arrival times, not a change of scheduler.

---

## Remedies

- **Analyse and enforce the task model.** Response-time analysis rules out starvation only if each task really is released no more often than once per $T_j$. Rate-limit interrupts and other sporadic sources, or handle them through a server with a fixed budget, so a burst cannot exceed what was analysed.
- **Execution budgets** stop a higher-priority task from running longer than its C.
- **Ageing** guarantees progress for low-priority work in general-purpose systems, but loses the fixed-priority analysis.
- **EDF** avoids indefinite waiting for any job, but under overload it spreads misses across every task: see the [domino effect](edf-domino.md).

??? question "Check your understanding"
    In the burst scenario, the task set passes response-time analysis with $T_1=8$. Why does $\tau_3$ still miss deadlines under FPS?

    The analysis assumes that $\tau_1$ is released at most once every 8 units. During the burst it is released every 2 units, so $\tau_1$ and $\tau_2$ together need $1/2+2/4=1$ of the processor, and $\tau_3$ gets nothing until the burst ends. The analysis was correct for its model; the system broke the model.

---

## Related Material

- [Fixed-priority scheduling and response-time analysis](../real-time-scheduling/fixed-priority.md)
- [EDF domino effect tutorial](edf-domino.md)
- [EDF and processor demand](../real-time-scheduling/edf.md)
- [Real-time systems: task models](../real-time-scheduling/introduction.md)
