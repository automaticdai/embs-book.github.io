# Mixed-Criticality Systems

*Lecture 18 · Mixed-Criticality Systems*

A mixed-criticality system hosts functions with different assurance requirements on one platform. For example, flight control and a route optimiser may share processing resources while requiring different guarantees.

**Criticality** expresses required assurance and the consequences of failure. **Priority** determines scheduling order. A high-criticality task need not always have the highest scheduling priority.

## Extend the task model

For two criticality levels, a task has a level $L_i\in\{\mathrm{LO},\mathrm{HI}\}$ and execution budgets associated with the analysis level. A high-criticality task has $C_i(\mathrm{LO})\le C_i(\mathrm{HI})$.

The larger value reflects more conservative execution assumptions or stronger assurance. It does not mean the same instruction intrinsically runs slower when criticality changes.

The following lecture methods assume independent, preemptive tasks on a single processor with fixed priorities, constrained deadlines, no blocking or self-suspension, and execution monitoring where required. Their guarantees depend on both the analysis and the runtime enforcing the budget policy.

## Three approaches in the lecture

| Method | Treatment of low-criticality execution | Consequence |
|---|---|---|
| 1: Multiple analysis levels | Analyse higher-priority interference using budgets at the analysed task's criticality level | Requires high-assurance budgets even for interfering low-criticality work |
| 2: Enforce each task's budget | Cap low-criticality work at its LO budget; allow HI work its HI budget | Needs runtime enforcement; reduces interference attributed to LO tasks |
| 3: Change mode | Begin with LO budgets; abandon LO service when a HI job needs more than its LO budget | Must analyse both normal operation and the transition to HI mode |

Abandoning low-criticality service is the policy of this mathematical model. A real application must specify the acceptable degraded behaviour and what happens to shared state and resources at the transition.

## Analyse the mode transition

First compute LO-mode response times using LO budgets:

$$R_i^{\mathrm{LO}}=C_i(\mathrm{LO})+\sum_{j\in hp(i)}\left\lceil\frac{R_i^{\mathrm{LO}}}{T_j}\right\rceil C_j(\mathrm{LO}).$$

For a HI task, a sufficient adaptive mixed-criticality response bound is

$$R_i^{\mathrm{AMC}}=C_i(\mathrm{HI})
+\sum_{j\in hp_{\mathrm{HI}}(i)}\left\lceil\frac{R_i^{\mathrm{AMC}}}{T_j}\right\rceil C_j(\mathrm{HI})
+\sum_{j\in hp_{\mathrm{LO}}(i)}\left\lceil\frac{R_i^{\mathrm{LO}}}{T_j}\right\rceil C_j(\mathrm{LO}).$$

The low-criticality contribution is bounded using the LO response window. It is not zero: low-criticality work may already have run before the mode change. Solve the bound iteratively and require the relevant LO and HI bounds to meet deadlines. This is a sufficient analysis and can be pessimistic; see [Baruah, Burns and Davis (2011)](https://www-users.york.ac.uk/~rd17/papers/MC_RTSS2011.pdf).

## Worked example: 150, 45, then 34

The lecture assigns priorities in task-number order, with task 1 highest:

| Task | Criticality | $C(\mathrm{LO})$ | $C(\mathrm{HI})$ | $T$ | $D$ |
|---|---|---:|---:|---:|---:|
| 1 | LO | 1 | 2 in method 1 only | 3 | 1 |
| 2 | HI | 1 | 2 | 10 | 10 |
| 3 | HI | 10 | 20 | 200 | To be determined |

Method 1 analyses task 3 with interference costs 2 and 2:

$$R_3=20+2\lceil R_3/3\rceil+2\lceil R_3/10\rceil=150.$$

Method 2 caps task 1 at one execution unit:

$$R_3=20+\lceil R_3/3\rceil+2\lceil R_3/10\rceil=45.$$

For method 3, the LO iteration gives $10\to15\to17\to18\to18$, so $R_3^{\mathrm{LO}}=18$. Then

$$R_3^{\mathrm{AMC}}=20+2\lceil R_3^{\mathrm{AMC}}/10\rceil+\lceil18/3\rceil=34.$$

These are the least fixed points of the respective recurrences. If task 3's deadline is 40, methods 1 and 2 do not establish its schedulability, whereas method 3 does, provided the other tasks also pass and its runtime policy is enforced. The different results arise from different execution policies and interference assumptions.

## Priority assignment

Deadline monotonic assignment loses its general optimality for this extended task model. The lecture uses [Audsley's algorithm](../real-time-scheduling/fixed-priority.md#blocking-and-priority-search) to search from the lowest priority upward. Its guarantee depends on the selected mixed-criticality test satisfying the algorithm's compatibility conditions.

??? question "Check your understanding"
    Why is it unsafe to analyse HI mode using only high-criticality tasks and ignore the transition?

    Low-criticality jobs may have used processor time before the switch, delaying a high-criticality job that is still active. The bound must cover this carry-over delay as well as execution after the switch.
