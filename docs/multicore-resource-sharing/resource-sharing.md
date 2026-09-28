# Resource Sharing and Blocking

*Lecture 16 · Resource Sharing Protocols*

Tasks need mutual exclusion when concurrent access could corrupt a shared object or interfere with a peripheral operation. A **critical section** is the region that accesses a protected resource. Waiting for a lower-priority task to finish such a section adds blocking to the timing analysis.

## Priority inversion

Consider priorities $H>M>L$. Task L locks a resource, then H arrives and requests it. H blocks. If M now pre-empts L, H is indirectly delayed by M even though M has no use for the resource.

Without a suitable protocol, this delay is not bounded by the resource's critical-section length alone. Repeated medium-priority work can postpone the lock holder indefinitely if its arrivals are unconstrained.

## Priority inheritance

Under the **priority inheritance protocol (PIP)**, a task holding a resource inherits the priority of a higher-priority task blocked by it. In the example, L runs at H's priority until it releases the resource, preventing M from extending that particular blocking interval.

Inheritance can propagate through nested blocking relationships. PIP does not prevent deadlock: two tasks can still hold different locks while each waits for the other's. A task can also suffer several blocking intervals, so a single maximum critical section is not a general PIP blocking bound.

## Resource ceilings

For a resource $r$, its ceiling $\Pi(r)$ is the highest base priority of any task that may use it. The protocols below assume one processor, known resource users, properly nested locking, no suspension inside critical sections, and the protocol's prescribed scheduling rules.

| Protocol | When priority changes | Admission rule |
|---|---|---|
| Original ceiling priority protocol (OCPP, also called original PCP) | A lock holder inherits when it blocks higher-priority work | A task may lock only if its dynamic priority exceeds the ceilings of resources held by other tasks |
| Immediate ceiling priority protocol (ICPP) | Immediately on locking, to at least the resource ceiling | Execution at ceiling priority prevents conflicting preemption; equal-priority work must not pre-empt the holder |

These ceiling protocols prevent deadlock and bound lower-priority blocking to at most one critical section under their assumptions. ICPP can delay a task before it first executes, even if that task does not directly request the occupied resource.

## Compute the blocking bound

Let $S_{j,r}$ bound the execution time for lower-priority task $j$'s critical section on resource $r$, including nested execution where applicable. For the single-processor ceiling model:

$$B_i=\max\bigl(\{S_{j,r}:j\in lp(i),\ \Pi(r)\ge P_i\}\cup\{0\}\bigr).$$

Consider all lower-priority sections whose resource ceiling reaches task i's priority. Restricting the search to resources used directly by i can miss ceiling blocking.

!!! example "Illustrative ceiling calculation"
    H, M and L have priorities 3, 2 and 1. H and L use Q, so Q's ceiling is 3; M and L use V, so V's ceiling is 2. L's sections on Q and V take at most 2 ms and 4 ms. Assume these are the only lower-priority sections relevant to the calculation.

    H can be blocked by Q, giving $B_H=2$ ms. M can be blocked by either Q or V, giving $B_M=4$ ms, even though M never uses Q. Under the ceiling protocol these bounds take a maximum, rather than the sum $2+4$.

## Put blocking into response-time analysis

Use the protocol's bound in

$$w_i^{(k+1)}=C_i+B_i+\sum_{j\in hp(i)}\left\lceil\frac{w_i^{(k)}}{T_j}\right\rceil C_j.$$

The task's own critical-section execution belongs in $C_i$; $B_i$ represents relevant lower-priority blocking. Higher-priority critical-section execution is already part of higher-priority $C_j$. Avoid counting the same execution twice.

Keep sections short, list every possible resource user and document nesting. [FreeRTOS mutexes](../rtos/freertos.md) implement priority inheritance, so they should not be analysed as if they automatically implement a ceiling protocol.

??? question "Check your understanding"
    Can priority inheritance resolve a circular wait in which A holds Q and needs V, while B holds V and needs Q?

    No. Raising priorities does not release either lock. Use an appropriate deadlock-preventing protocol or a consistent resource acquisition order.

Continue with [multicore scheduling](multicore.md): single-processor blocking bounds cannot simply be reused across cores.
