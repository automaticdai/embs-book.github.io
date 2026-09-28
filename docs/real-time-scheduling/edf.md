# Earliest Deadline First and Processor Demand

*Lecture 15 · EDF*

**Earliest Deadline First (EDF)** executes the ready job with the earliest absolute deadline. A job released at $r_{i,k}$ has absolute deadline $d_{i,k}=r_{i,k}+D_i$. Job priorities can therefore differ from the ordering of task periods or relative deadlines.

The results here assume one processor, independent periodic/sporadic tasks, preemptive execution, bounded execution costs, no jitter or self-suspension, and zero or safely modelled overhead. EDF is optimal for feasible collections of independent preemptible jobs on one processor. The same claim does not hold for global EDF on several processors.

## Utilisation and density

For implicit deadlines, the exact test is

$$U=\sum_i C_i/T_i\le1.$$

For constrained deadlines, $\sum_i C_i/D_i\le1$ is sufficient, but not necessary. Utilisation alone is no longer enough: two tasks with $C=2$, $D=2$, $T=10$ use only 40% of the processor on average, but demand four units by time 2 if released together.

## Count work that must finish

The demand bound function counts jobs whose release and deadline can both lie within an interval of length $t$:

$$h(t)=\sum_i\max\left(0,\left\lfloor\frac{t-D_i}{T_i}\right\rfloor+1\right)C_i.$$

EDF is schedulable for this task model exactly when $h(t)\le t$ for every $t>0$. This function includes arbitrary deadlines as well as constrained deadlines. The zero clamp prevents negative demand before a task's first deadline.

Demand increases only at points $D_i+kT_i$, for integers $k\ge0$. Between these points, demand is constant and processor supply increases. **Processor Demand Analysis (PDA)** therefore checks candidate deadline points within a justified finite interval.

## Bound the search

For constrained deadlines and $U<1$, the lecture uses

$$L_a=\max\left(\max_iD_i,\frac{\sum_i(T_i-D_i)U_i}{1-U}\right).$$

Alternatively, find the synchronous busy-period bound by iterating

$$w^{(0)}=\sum_iC_i,\qquad w^{(k+1)}=\sum_i\left\lceil\frac{w^{(k)}}{T_i}\right\rceil C_i.$$

At convergence, $L_b=w^{(k+1)}=w^{(k)}$. When both bounds apply, use $L=\min(L_a,L_b)$ and check deadlines up to and including $L$.

The $L_a$ expression above must not be used for arbitrary deadlines or at $U=1$. The interactive tutorial uses the busy-period route for arbitrary deadlines and generates workloads with $U<1$. An implementation that hits an iteration limit has an inconclusive computation, not a proof of schedulability.

## Worked example from the lecture

| Task | $C_i$ | $T_i$ | $D_i$ |
|---|---:|---:|---:|
| A1 | 1 | 3 | 2 |
| A2 | 2 | 5 | 4 |
| A3 | 3 | 14 | 12 |

Here $U=199/210\approx0.948$, $L_a=244/11\approx22.18$, and the busy-period iteration is $6\to9\to10\to11\to13\to14\to14$. Thus $L=14$.

| Candidate $t$ | 2 | 4 | 5 | 8 | 9 | 11 | 12 | 14 |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| Demand $h(t)$ | 1 | 3 | 4 | 5 | 7 | 8 | 11 | 14 |

Every point passes, so the set is schedulable under the stated model. Equality at 14 is allowed: all required work finishes at its deadline.

## QPA: skip intervals that cannot fail

**Quick convergence Processor-demand Analysis (QPA)** checks the same condition by moving backward from $L$:

1. If $h(t)>t$, reject the task set.
2. If $h(t)<t$, jump to $t=h(t)$.
3. If $h(t)=t$, move to the largest candidate deadline strictly below $t$.
4. Accept when the next point is below the smallest relative deadline.

The jump is safe because, for $h(t)\le s\le t$, monotonicity gives $h(s)\le h(t)\le s$. An equality requires moving backward so the algorithm does not stall.

For the lecture example, QPA evaluates $14,12,11,8,5,4,3$, then stops at 1. PDA evaluates eight deadline points; QPA evaluates seven points here. The saving depends on the task set, and can be much larger.

Try the [PDA vs QPA explorer](../tutorials/pda-qpa.md) and compare the traces. Under overload, EDF alone does not determine which application functions should retain service; [mixed-criticality scheduling](../multicore-resource-sharing/mixed-criticality.md) introduces an explicit service policy.

??? question "Check your understanding"
    Why does $h(3)=1$ in the example even though all three tasks may have released at time 0?

    Only A1 has a deadline by time 3. PDA counts work required to finish within the interval, rather than all work released by its end.
