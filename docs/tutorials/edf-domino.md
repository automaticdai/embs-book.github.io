# The EDF Domino Effect

This interactive tutorial shows what happens to **Earliest Deadline First (EDF)** scheduling when there is more work than time. You can replay the domino example from the EDF lecture, overload a periodic task set, and compare EDF with fixed-priority scheduling (FPS) and with EDF plus admission control on the same jobs.

!!! info "What you'll learn"
    - Why EDF's optimality says nothing about behaviour under overload
    - How one extra job, or one job that overruns its C, can make a whole chain of other jobs miss their deadlines
    - Why EDF minimises the *maximum lateness* and not the *number* of late jobs
    - Why FPS is more predictable under overload: the lowest-priority tasks miss first
    - How admission control and aborting late jobs limit the damage

---

## Background

For independent preemptible jobs on one processor, EDF is optimal: if any scheduler can meet every deadline, EDF can too. For periodic tasks with implicit deadlines, that means every deadline is met whenever $U\le1$ ([EDF and processor demand](../real-time-scheduling/edf.md)).

Optimality is a statement about *feasible* workloads. It promises nothing once demand exceeds supply, which can happen if a job overruns its estimated C, an unexpected job arrives, or a burst of events breaks the assumed minimum inter-arrival time. The FPS lecture compares the two policies like this:

- **FPS is more predictable under overload.** Low-priority tasks miss their deadlines first.
- **EDF is unpredictable under overload.** A **domino effect** can occur in which many tasks miss their deadlines.

The reason is that EDF priorities follow deadlines, not importance. A job close to its deadline is always favoured, even when it is already doomed, and the delay is passed on to every job behind it. Dertouzos (1974) showed that EDF minimises the lateness of the *most tardy* job. It therefore spreads lateness out evenly, so many jobs become slightly late instead of a few becoming very late.

### The lecture example

The EDF lecture illustrates this with four jobs that each arrive with an earlier deadline than the one before, followed by a newcomer $\tau_{new}$. The slide gives no numbers. The values below reproduce the shape of its figure.

| Job | Release $r$ | $C$ | Relative deadline $D$ | Absolute deadline $d=r+D$ |
|---|---:|---:|---:|---:|
| $\tau_4$ | 0 | 3 | 13 | 13 |
| $\tau_3$ | 2 | 3 | 10 | 12 |
| $\tau_2$ | 4 | 3 | 7 | 11 |
| $\tau_1$ | 6 | 3 | 4 | 10 |
| $\tau_{new}$ | 7 | 2 | 2 | 9 |

Without $\tau_{new}$, each new arrival preempts the previous job, and the jobs finish at 9, 10, 11 and 12 ($\tau_1$ first). Each one meets its deadline with exactly one unit to spare.

$\tau_{new}$ adds 2 units of work and has the earliest deadline, so EDF runs it at once. Every unfinished job is pushed back by 2 units but had only 1 unit of slack: $\tau_1$ finishes at 11, $\tau_2$ at 12, $\tau_3$ at 13 and $\tau_4$ at 14. **Four jobs miss their deadlines, each by 1**, while the job that caused the overload meets its own.

---

## How to Use

**Scenario** loads one of three task sets:

- **One late arrival (lecture)**: the five jobs in the table above
- **One overrun (periodic)**: four periodic tasks with $U=0.95$. The first job of $\tau_4$ runs 4 units longer than its C
- **Permanent overload**: three periodic tasks with $U=1.15$

**Scheduler** switches between EDF, FPS (using the priority column, 1 = highest) and EDF with **admission control**. Admission control rejects an arriving job if accepting it would make an accepted job miss its deadline. The test uses the declared C of each job, because the scheduler cannot know in advance that a job will overrun.

**A job that misses its deadline** either *keeps running* until it finishes, or *is aborted* at its deadline.

**The chart** shows one row per task. Arrows mark releases. Hatched bars under the timeline show a task that has a ready job but is not running. Red hatching marks execution after the job's deadline, and dashed red circles mark missed deadlines.

**Play**, **Step** and the slider replay the schedule one time unit at a time, with an explanation of each decision. Click the chart to jump to a time.

**Edit the task set** to change any parameter. Untick *Use* to remove a task temporarily, and use the *Overrun* controls to make any job run longer than its C.

The **Same tasks, every scheduler** table re-runs the current task set under each scheduler. Click a row to switch to it.

---

## Interactive Tutorial

<a href="../embeds/edf-domino.html" target="_blank" rel="noopener" class="md-button">&#x2197; Open in new window</a>

<iframe src="../embeds/edf-domino.html" title="Interactive EDF domino effect simulator" class="tutorial-frame" width="100%" height="2100" loading="lazy"></iframe>

---

## Key Observations

!!! example "Things to try"
    1. **Lecture example.** Play the schedule under EDF and watch $\tau_{new}$ preempt $\tau_1$ at $t=7$. Four deadlines are missed, each by 1. Untick $\tau_{new}$ and every deadline is met again.
    2. **Who should lose?** Switch to FPS. $\tau_{new}$ has the lowest priority (5), so only $\tau_{new}$ misses, by 5 units. EDF has the smaller maximum lateness (1 against 5), as Dertouzos' result predicts, but FPS has fewer late jobs (1 against 4). Now give $\tau_{new}$ priority 1 and FPS behaves exactly like EDF, because the priority order then matches the deadline order.
    3. **Admission control.** Choose EDF + admission control. $\tau_{new}$ is rejected when it arrives and the other four jobs all meet their deadlines.
    4. **One overrun.** In the periodic scenario, EDF gives 12 missed deadlines spread over $\tau_1$, $\tau_2$ and $\tau_3$, while $\tau_4$, the task that overran, misses none. Under rate-monotonic FPS, only $\tau_4$ misses (3 jobs). Set the overrun to 0 to confirm that the task set is schedulable under both.
    5. **Admission control cannot see overruns.** With EDF + admission control in the periodic scenario, 3 deadlines are still missed. The overrun happens after the job has been accepted, so the test, which uses the declared C, cannot prevent it.
    6. **Abort late jobs.** Switch *A job that misses its deadline* to *is aborted*. In the lecture example only $\tau_1$ is lost. In the periodic scenario, EDF misses drop from 12 to 2. A job that cannot finish in time no longer takes processor time from jobs that still can.
    7. **Permanent overload.** With $U=1.15$, EDF misses deadlines in every task and the lateness keeps growing. FPS keeps $\tau_1$ and $\tau_2$ on time, and $\tau_3$ receives only 6 of the 15 units it needs. That is the starvation problem studied in the [FPS starvation tutorial](fps-starvation.md).

---

## Remedies

The domino effect follows from running EDF when demand exceeds supply, so the remedies keep the scheduler out of overload or decide in advance who loses:

- **Admission control** (an on-line guarantee test) rejects new work that would cause a miss. It protects accepted jobs from new arrivals, but not from accepted jobs that overrun.
- **Aborting or skipping late jobs** stops a doomed job from consuming time that later jobs need.
- **Execution budgets and servers** stop a task from using more than its declared C, so its overrun cannot spill onto other tasks.
- **Value-based** and **[mixed-criticality](../multicore-resource-sharing/mixed-criticality.md)** scheduling state explicitly which functions keep their service during overload.

??? question "Check your understanding"
    In the lecture example, $\tau_{new}$ adds only 2 units of work, yet four jobs miss their deadlines. Why does EDF not let $\tau_{new}$ miss instead, given that this would mean one late job instead of four?

    EDF ranks jobs only by absolute deadline. $\tau_{new}$ has the earliest deadline (9), so it always runs first. EDF minimises the maximum lateness: four jobs each 1 late is better, by that measure, than one job 5 late. Minimising the number of late jobs is a different objective, and EDF does not pursue it.

---

## Related Material

- [EDF and processor demand](../real-time-scheduling/edf.md)
- [Fixed-priority scheduling](../real-time-scheduling/fixed-priority.md)
- [FPS starvation tutorial](fps-starvation.md)
- [Mixed-criticality systems](../multicore-resource-sharing/mixed-criticality.md)
