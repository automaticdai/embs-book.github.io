# PDA vs QPA Interactive Explorer

## EDF Schedulability Tests — Interactive Comparison

This interactive explorer compares two exact schedulability tests for **Earliest Deadline First (EDF)** scheduling on a single processor: **Processor Demand Analysis (PDA)** and **Quick convergence Processor-demand Analysis (QPA)**.

Both tests give the same verdict. The difference is how many times each one evaluates the demand function $h(t)$.

!!! info "What you'll learn"
    - What the processor demand function $h(t)$ measures
    - How the upper bound $L$ on the testing interval is computed
    - Why PDA checks every absolute deadline up to $L$
    - How QPA sweeps backwards from $L$ and skips most of those points

---

## Background

For a task set with worst-case execution times $C_i$, periods $T_i$ and relative deadlines $D_i$, the **processor demand** in the interval $[0, t]$ is:

$$h(t) = \sum_{i:\, D_i \le t} \left( \left\lfloor \frac{t - D_i}{T_i} \right\rfloor + 1 \right) C_i$$

For independent preemptible periodic/sporadic tasks on one processor, with no jitter or self-suspension and zero or safely modelled overhead, PDA checks $h(t)\le t$ at every candidate deadline $0<t\le L$, where $L$ is a valid analysis bound. The explorer generates task sets with $U<1$. See [EDF and processor demand](../real-time-scheduling/edf.md) for the bound assumptions and a worked lecture example.

- **PDA** tests every absolute deadline in $(0, L]$ in increasing order.
- **QPA** (Zhang & Burns, 2009) starts at $L$ and repeatedly sets $t \leftarrow h(t)$ while $h(t) < t$, jumping over deadlines that cannot fail. It usually needs far fewer evaluations.

---

## How to Use

- **tasks (n)** sets the number of tasks
- **target utilisation** sets the total utilisation $U$ of the generated task set
- **periods** selects the range that task periods are drawn from
- **arbitrary deadlines** allows deadlines greater than periods ($D > T$)
- **regenerate** draws a new random task set

The chart plots $h(t)$ against the line $y = t$. PDA testing points are shown as dots and the QPA trace as diamonds. The **speedup** metric is the ratio of PDA evaluations to QPA evaluations.

---

## Interactive Explorer

<a href="../embeds/pda-qpa.html" target="_blank" rel="noopener" class="md-button">&#x2197; Open in new window</a>

<iframe src="../embeds/pda-qpa.html" title="Interactive PDA vs QPA schedulability explorer" class="tutorial-frame" width="100%" height="1600" loading="lazy"></iframe>

---

## Key Observations

This tutorial accompanies **lecture 15**. Work through the [three-task PDA/QPA example](../real-time-scheduling/edf.md#worked-example-from-the-lecture) before experimenting with larger sets.

!!! example "Things to try"
    1. **Widen the period range** from narrow to extreme and watch the number of PDA testing points grow while QPA stays small.
    2. **Raise the utilisation** towards 0.95 — $L$ grows quickly, and so does the gap between the two tests.
    3. **Enable arbitrary deadlines** — $L_a$ is no longer used, so $L$ falls back to the synchronous busy period $L_b$.
    4. **Regenerate until you find an infeasible set** and see where $h(t)$ crosses above $y = t$.
