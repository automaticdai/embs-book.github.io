# Introduction to Real-Time Scheduling

*Lecture 13 · Real-Time Systems; overview of lectures 14–15*

## What is Real-Time Scheduling?

In a real-time system, correctness depends not only on the result of a computation but also on **when** it is produced. **Real-time scheduling** decides the order in which tasks execute so that every task meets its deadline, and **schedulability analysis** proves this before the system runs.

---

## Hard and Soft Real-Time

- **Hard real-time** — missing a deadline is a system failure (e.g., airbag deployment, flight control)
- **Soft real-time** — missing a deadline degrades quality but is tolerable (e.g., video streaming)
- **Firm real-time** — late results are useless but occasional misses are tolerable

---

## Task Model

Each periodic or sporadic task $\tau_i$ is commonly described by:

| Parameter | Meaning |
|-----------|---------|
| $C_i$ | Worst-case execution time (WCET) |
| $T_i$ | Period, or minimum inter-arrival time |
| $D_i$ | Relative deadline |
| $U_i = C_i / T_i$ | Utilisation |
| $R_i$ | Worst-case response time, from release to completion |
| $B_i$ | Bounded delay due to lower-priority blocking |
| $J_i$ | Release jitter, when included by the analysis |

Deadlines are **implicit** if $D_i = T_i$, **constrained** if $D_i \le T_i$, and **arbitrary** otherwise. The total utilisation is $U = \sum_i U_i$.

A **task** describes a stream of executions; a **job** is one execution. A job released at $r_{i,k}$ has absolute deadline $r_{i,k}+D_i$. Periodic jobs have fixed spacing, sporadic jobs have a minimum spacing, and aperiodic arrivals have no inherent periodicity. A worst-case timing guarantee requires a bound on arrivals as well as execution costs.

## Assumptions behind the basic tests

The tests below concern independent tasks on a single processor with fully preemptive execution, known WCETs, no self-suspension and no release jitter. Overheads are ignored or safely included in the model. The basic response-time recurrence assumes constrained deadlines. Shared resources, multicore interference and mode changes need additional analysis.

| Kind of test | Passing means | Failing means |
|---|---|---|
| Sufficient | Schedulability is established under the assumptions | Inconclusive |
| Necessary | Inconclusive | The task set cannot satisfy the model's requirements |
| Exact | Schedulable under the model | Unschedulable under the model |

A sustainable guarantee remains valid when conditions improve in the ways allowed by its model, such as reduced execution costs. Always state the particular assumptions and changes involved.

---

## Scheduling Approaches

| Approach | Priority | Examples |
|----------|----------|----------|
| **Cyclic executive** | None — a fixed, precomputed timetable | Safety-critical avionics |
| **Fixed-priority** | Assigned per task, offline | Rate Monotonic (RM), Deadline Monotonic (DM) |
| **Dynamic-priority** | Assigned per job, at runtime | Earliest Deadline First (EDF) |

Lecture 13 also introduces **Least Laxity First (LLF)**, where laxity is the time until the deadline minus the job's remaining execution, and **value-based scheduling**, which uses application value to guide decisions under overload. These policies answer different questions from a proof that all deadlines can be met.

!!! info "Key Insight"
    Under the independent preemptive single-processor model, RM is optimal among fixed-priority assignments for implicit deadlines, and DM for constrained deadlines. EDF is optimal for independent preemptible jobs on a single processor. These results do not automatically carry over to extended task models.

---

## Schedulability Tests

**Utilisation bound for RM** (Liu & Layland): a set of $n$ implicit-deadline tasks is schedulable if

$$U \le n\left(2^{1/n} - 1\right)$$

This test is sufficient but not necessary — task sets above the bound may still be schedulable.

**Response-time analysis** for fixed-priority scheduling: the worst-case response time $R_i$ of task $\tau_i$ is the smallest solution of

$$R_i = C_i + \sum_{j \in hp(i)} \left\lceil \frac{R_i}{T_j} \right\rceil C_j$$

where $hp(i)$ is the set of higher-priority tasks. The task set is schedulable if $R_i \le D_i$ for all tasks.

**EDF**: implicit-deadline tasks are schedulable if and only if $U \le 1$. For constrained or arbitrary deadlines, exact tests such as Processor Demand Analysis (PDA) and QPA are used.

!!! tip "Interactive Tutorial"
    Try the [**PDA vs QPA Interactive Explorer**](../tutorials/pda-qpa.md) to compare two exact EDF schedulability tests on random task sets.

---

## Next Steps

- Work through [fixed-priority response-time analysis and Audsley's priority assignment](fixed-priority.md)
- Calculate [EDF demand bounds and compare PDA with QPA](edf.md)
- See how tasks sharing resources affects these tests in [Multicore and Resource Sharing](../multicore-resource-sharing/introduction.md)
- Review the kernel mechanisms that implement scheduling in [Real-Time Operating Systems](../rtos/introduction.md)
