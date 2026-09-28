# Fixed-Priority Scheduling and Response-Time Analysis

*Lectures 13–14 · Real-Time Systems and FPS*

Fixed-priority scheduling assigns each task a priority; every job of that task uses the same base priority. On one processor, the highest-priority ready job executes and can pre-empt lower-priority work.

## State the assumptions first

The analysis below assumes independent periodic or sporadic tasks on one processor, fully preemptive execution, known WCETs, no release jitter or self-suspension, and constrained deadlines $0<C_i\le D_i\le T_i$. Scheduling overhead is ignored or safely accounted for. Each sporadic task's $T_i$ is its minimum separation between releases.

Under this model, a critical instant occurs when a task releases with all higher-priority tasks, which then release as frequently as permitted. Fixed offsets, blocking or other dependencies require the corresponding analysis.

## Priority assignment

**Rate monotonic (RM)** assigns higher priorities to shorter periods. It is optimal among fixed-priority assignments for the independent implicit-deadline model ($D_i=T_i$).

**Deadline monotonic (DM)** assigns higher priorities to shorter relative deadlines. It is optimal for the independent constrained-deadline model. These results concern fixed-priority assignments under their stated assumptions; they do not extend automatically to multicore or mixed-criticality systems.

## A quick sufficient test

For $n$ implicit-deadline tasks scheduled by RM:

$$U=\sum_i\frac{C_i}{T_i}\le n(2^{1/n}-1)$$

is sufficient for schedulability. The bound approaches $\ln 2\approx0.693$ as $n$ grows. A result above the bound is **inconclusive**, rather than proof of a deadline miss.

## Exact response-time analysis

For each task, start with $w_i^{(0)}=C_i$ and iterate

$$w_i^{(k+1)}=C_i+\sum_{j\in hp(i)}\left\lceil\frac{w_i^{(k)}}{T_j}\right\rceil C_j.$$

The ceiling counts how many releases of a higher-priority task can interfere before completion. Continue until consecutive values agree, giving $R_i$, or the value exceeds $D_i$. The task passes when the fixed point satisfies $R_i\le D_i$.

All tasks must pass. This test is exact for the stated independent task model; the resulting guarantee still depends on the validity of the execution-time bounds.

## Worked example: lecture task set D

All times use the same unit. Deadlines equal periods, and a larger priority number means a higher priority.

| Task | $C$ | $T=D$ | Priority |
|---|---:|---:|---:|
| a | 3 | 7 | 3 |
| b | 2 | 12 | 2 |
| c | 5 | 20 | 1 |

Task a has no higher-priority interference, so $R_a=3$. For b, the iteration is $2\to5\to5$, giving $R_b=5$.

For c:

$$w_c^{(k+1)}=5+\left\lceil\frac{w_c^{(k)}}7\right\rceil3+\left\lceil\frac{w_c^{(k)}}{12}\right\rceil2.$$

| Current estimate | Next estimate |
|---:|---:|
| 5 | 10 |
| 10 | 13 |
| 13 | 15 |
| 15 | 18 |
| 18 | 18 |

All responses meet their deadlines: $(3,5,18)\le(7,12,20)$. Yet $U\approx0.845$ exceeds the three-task utilisation bound of about 0.780. This is why the utilisation test's failure cannot be treated as rejection.

## Blocking and priority search

With an appropriate resource protocol, include a safe blocking bound:

$$w_i^{(k+1)}=C_i+B_i+\sum_{j\in hp(i)}\left\lceil\frac{w_i^{(k)}}{T_j}\right\rceil C_j.$$

Start at $C_i+B_i$. Deriving $B_i$ requires the [resource access protocol](../multicore-resource-sharing/resource-sharing.md); it is not an arbitrary extra margin.

**Audsley's priority assignment** works from the lowest unassigned priority upward. Test each remaining task at that priority with all other unassigned tasks above it; fix a passing task there and repeat. Its optimality requires a compatible schedulability test, including independence from the relative order of higher-priority tasks. A failed attempt to find any candidate rejects all assignments only when those conditions hold.

??? question "Check your understanding"
    Keep the priorities and costs of task set D, but reduce c's deadline to 17. Does it pass this fixed-priority assignment?

    No. The response-time iteration reaches 18, exceeding 17. The unchanged total utilisation alone cannot reveal the effect of the tighter deadline.

Continue with [EDF and processor demand](edf.md) to analyse dynamic job priorities.
