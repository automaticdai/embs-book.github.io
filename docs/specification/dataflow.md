# Kahn Process Networks and Synchronous Dataflow

*Lecture 7 · Dataflow Models*

Dataflow makes communication dependencies explicit. The implementation must preserve the token sequences while providing a schedule and enough storage for the channels.

## Kahn process networks

A KPN consists of deterministic sequential processes connected by conceptually unbounded FIFO channels. Each channel has one writer and one reader. A read from an empty channel blocks; a write does not block in the abstract model. A process cannot choose its behaviour by testing which input happens to have data available.

These rules make the output token sequences deterministic for given inputs, independent of execution speed or process interleaving. This does not guarantee that execution finishes, avoids deadlock or uses bounded memory. Starving a runnable process also prevents progress.

An implementation has finite buffers. If writes block because those buffers fill, it introduces behaviour absent from the unbounded model. Buffer sizing and scheduling are therefore part of the implementation argument. Bounded-memory execution is undecidable for general KPNs.

!!! tip "Interactive tutorial"
    Be the scheduler for the lecture's even/odd network in the [KPN tutorial](../tutorials/kpn-scheduler.md). Fire the processes in any order and the output does not change; switch the merge to “first available” and it does.

## The SDF restriction

In **synchronous dataflow**, every actor consumes and produces a fixed, known number of tokens on each port per firing. These rates permit static analysis. Here “synchronous” refers to predictable token rates; it does not require all actors to run at the same physical instant.

A **periodic admissible sequential schedule (PASS)** is a finite firing sequence that can be repeated. It must satisfy both token balance over a period and token availability at every firing.

## Balance equations

For an edge from actor $A$ to actor $B$, with production rate $p$ and consumption rate $c$, the firing counts must satisfy

$$p q_A-c q_B=0.$$

Collect one equation per edge in the **topology matrix** $\Gamma$. A repetition vector $q$ satisfies

$$\Gamma q=0,\qquad q_i\in\mathbb{Z}_{>0}.$$

Use the smallest positive integer solution for a basic iteration. For a connected graph with ordinary positive rates, consistency gives a one-dimensional nullspace and $\operatorname{rank}(\Gamma)=n-1$. Disconnected components must be considered separately. Token balance alone does not prove that an executable schedule exists.

## Worked example: the overheat monitor as SDF

The dataflow version of the [overheat monitor](models-of-computation.md#running-example-an-overheat-monitor) is a chain:

| Channel | Produced per source firing | Consumed per destination firing |
|---|---:|---:|
| Sensor $\to$ Average | 1 | 4 |
| Average $\to$ Compare | 1 | 1 |
| Compare $\to$ LED | 1 | 1 |

The Average actor takes non-overlapping blocks of four samples. That multirate behaviour is what gives the graph a rate other than 1. The balance equations $q_S=4q_A$, $q_A=q_C$ and $q_C=q_L$ give the repetition vector $q=(4,1,1,1)$ for (Sensor, Average, Compare, LED).

Starting with empty channels, `S S S S A C L` is a PASS with a peak of four tokens on Sensor $\to$ Average. `S S S S A S S S S A C C L L` is also valid but repeats the basic iteration twice and holds two averages at once. With samples 70, 75, 82, 88, 90, 92, 85, 79, 60, 55, 58, 62, the averages are 78.75, 86.5 and 58.75 °C, and the LED sees `off, on, off` whichever valid order is used. This is Kahn determinism.

The DE version used a sliding window, so it produces an average for every sample. Changing the averager changes the function computed, not just its schedule.

Try both orders in the [interactive tutorial](../tutorials/moc-overheat-monitor.md).

## Worked example: the lecture's three actors

The channels have these rates:

| Channel | Produced per source firing | Consumed per destination firing |
|---|---:|---:|
| $A\to B$ | 1 | 1 |
| $A\to C$ | 2 | 1 |
| $B\to C$ | 2 | 1 |

Thus

$$\Gamma=\begin{bmatrix}1&-1&0\\2&0&-1\\0&2&-1\end{bmatrix},\qquad q=\begin{bmatrix}1\\1\\2\end{bmatrix}.$$

With initially empty channels, `A B C C` is a PASS:

| After firing | $A\to B$ tokens | $A\to C$ tokens | $B\to C$ tokens |
|---|---:|---:|---:|
| Initially | 0 | 0 | 0 |
| A | 1 | 2 | 0 |
| B | 0 | 2 | 2 |
| C | 0 | 1 | 1 |
| C | 0 | 0 | 0 |

The schedule returns every buffer to its initial occupancy. `A C B C` has the same firing counts but fails: the first firing of C has no token from B.

## Initial tokens and deadlock

In a cycle `A → B → A` where each firing consumes and produces one token, $q=(1,1)$ balances the graph. If both channels start empty, neither actor can fire. A suitable initial token breaks the dependency cycle. Initial tokens model delay/state and influence the computed result, so their values matter as well as their number.

The [Ptolemy SDF scheduler](https://ptolemy.berkeley.edu/ptolemyII/ptII11.0/ptII11.0.1/doc/codeDoc/ptolemy/domains/sdf/kernel/SDFScheduler.html) likewise separates solving balance equations from finding an executable order.

## Schedule order changes memory use

Lecture 7 also uses the following five-actor graph:

| Channel | Production | Consumption |
|---|---:|---:|
| $A\to E$ | 2 | 1 |
| $A\to C$ | 1 | 1 |
| $B\to C$ | 2 | 2 |
| $B\to D$ | 2 | 1 |
| $C\to D$ | 2 | 1 |
| $D\to E$ | 3 | 3 |

The repetition vector is $(1,1,1,2,2)$ for $(A,B,C,D,E)$. Starting with empty channels, both `A B C D D E E` and `A B C D E D E` are valid. The first stores six tokens on $D\to E$; the second needs only three there, and at most three on every channel.

Grouping firings may reduce code or reconfiguration overhead, while interleaving firings may reduce buffer capacity. Both are [design space exploration](../hw-sw-codesign/design-space-exploration.md) choices.

??? question "Check your understanding"
    Actor A produces three tokens per firing and B consumes two. Find the smallest repetition vector and a valid schedule for the single channel, initially empty.

    $3q_A=2q_B$, so $q_A=2$ and $q_B=3$. `A B A B B` is valid and has a peak occupancy of four tokens. `A A B B B` is also valid but has a peak of six.
