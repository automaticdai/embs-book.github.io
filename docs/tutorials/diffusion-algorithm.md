# Diffusion Load Balancing Interactive Tutorial

## Diffusion Algorithm — Interactive Case Study

The **diffusion algorithm** (Boillat) is a dynamic mapping method for load balancing. Each processor repeatedly passes load to its immediate neighbours, in the same way that heat flows from hot regions to cold ones, until every processor holds the same amount. A processor only needs its own load and its neighbours' loads, so the algorithm runs in a distributed way with no central scheduler.

This tutorial starts from the four-processor example in the lecture slides and lets you change the graph, the diffusion coefficient $\alpha$ and the migration granularity.

!!! info "What you'll learn"
    - How each node's new load is calculated from its neighbours' loads alone
    - Why total load is conserved at every step
    - Why $\alpha$ must be small enough, and what happens with the lecture's $\alpha = 0.5$
    - How the graph's structure decides how quickly the imbalance disappears
    - Why moving only whole tasks can stop the algorithm before the loads are balanced

---

## How It Works

Let processor $i$ hold load $l_i(k)$ at step $k$. Along every link $(i, j)$ the algorithm moves an amount proportional to the load difference:

$$\Delta_{ij} = \alpha_{ij}\,\bigl(l_i(k) - l_j(k)\bigr)$$

A positive $\Delta_{ij}$ moves load from $i$ to $j$, and a negative one moves it from $j$ to $i$. The new load of processor $i$ is

$$l_i(k+1) = l_i(k) - \sum_{j \in N(i)} \Delta_{ij} = l_i(k) + \sum_{j \in N(i)} \alpha_{ij}\,\bigl(l_j(k) - l_i(k)\bigr)$$

where $N(i)$ is the set of neighbours of $i$. Two details matter:

- **Synchronous update.** Every $\Delta_{ij}$ is computed from the step-$k$ loads before any node changes. Updating nodes one at a time gives a different algorithm.
- **Conservation.** Whatever $i$ sends to $j$, $j$ receives, because $\Delta_{ji} = -\Delta_{ij}$ when $\alpha_{ij} = \alpha_{ji}$. The total load therefore never changes, and the balanced state gives every node the average load.

### Choosing α

The lecture gives two convergence conditions for a connected, undirected graph:

| Choice | Formula | Notes |
|---|---|---|
| One α for every edge | $\alpha = \dfrac{1}{\Delta + 1}$, where $\Delta$ is the maximum degree | Needs to know the maximum degree of the whole graph |
| One α per edge | $\alpha_{ij} = \dfrac{1}{\max(\deg i, \deg j) + 1}$ | Each pair of nodes only needs to know its own degrees |

Both choices keep a positive share of each node's own load: node $i$ keeps the fraction $1 - \sum_{j} \alpha_{ij} \ge \frac{1}{\Delta + 1}$ of its load. The new load is then a weighted average of the old loads of $i$ and its neighbours, so no node can overshoot past its neighbours and no load can become negative.

### How fast it converges

The update can be written as $\mathbf{l}(k+1) = W\,\mathbf{l}(k)$ with $W = I - \alpha L$, where $L$ is the graph Laplacian. The balanced vector is unchanged by $W$ (eigenvalue 1). Every other part of the imbalance is multiplied by one of the other eigenvalues of $W$ at each step, so the imbalance shrinks roughly like $\gamma^k$, where

$$\gamma = \max \bigl\{ |\mu| : \mu \text{ an eigenvalue of } W,\ \mu \ne 1 \bigr\}.$$

If $\gamma < 1$ the loads converge, and a smaller $\gamma$ means faster convergence. If $\gamma \ge 1$ they never settle. The tutorial shows $\gamma$ in its status line and plots the prediction $\sigma_0 \gamma^k$ next to the measured imbalance.

---

## Worked Example from the Lecture

The lecture graph has four processors. B is linked to A, C and D, and C is linked to D. The initial loads are A = 14, B = 4, C = 7, D = 1, a total of 26, so the balanced state is 6.5 each. B has the highest degree, 3.

**With the lecture's α = 0.5 on every edge**, the first step moves 5 from A to B, 1.5 from C to B, 1.5 from B to D and 3 from C to D:

| Step | A | B | C | D | σ |
|---:|---:|---:|---:|---:|---:|
| 0 | 14 | 4 | 7 | 1 | 4.822 |
| 1 | 9 | 9 | 2.5 | 5.5 | 2.716 |
| 2 | 9 | 4 | 7.25 | 5.75 | 1.846 |
| 3 | 6.5 | 9 | 4.875 | 5.625 | 1.554 |
| 4 | 7.75 | 4 | 7.3125 | 6.9375 | 1.472 |

B swings between 9 and 4 and keeps doing so: $\sigma$ levels off at about 1.44 instead of reaching 0. Here $\alpha = 0.5$ is larger than $1/(\Delta + 1) = 1/4$. B, with three neighbours, keeps $1 - 3 \times 0.5 = -0.5$ of its own load, a negative weight, so its update overshoots the average every time. One eigenvalue of $W$ is $-1$, so $\gamma = 1$. This is why the lecture ends with the convergence condition.

**With α = 1/4 on every edge**, $\gamma = 0.75$:

| Step | A | B | C | D | σ |
|---:|---:|---:|---:|---:|---:|
| 0 | 14 | 4 | 7 | 1 | 4.822 |
| 1 | 11.5 | 6.5 | 4.75 | 3.25 | 3.107 |
| 2 | 10.25 | 6.5 | 4.8125 | 4.4375 | 2.300 |
| 3 | 9.3125 | 6.5 | 5.1406 | 5.0469 | 1.723 |

After the first step, $\sigma$ falls by roughly a factor of 0.75 per step, matching $\gamma$. B reaches 6.5 in one step and stays there. All loads are within 0.005 of 6.5 after 26 steps. Convergence is guaranteed, but it is slow, as the lecture warns.

**With whole tasks only** (each $\Delta_{ij}$ rounded down to an integer) and α = 1/4, the loads go $(14, 4, 7, 1) \to (12, 6, 6, 2) \to (11, 6, 5, 4) \to (10, 7, 5, 4)$ and then stop. Every remaining difference across an edge is less than 4, so $\frac14$ of it rounds down to 0 tasks. This is the lecture's **granularity problem**: real tasks cannot be split, so the fine-grained transfers that diffusion asks for may not be possible.

---

## How to Use

Each processor is a round bucket. Its fill level is its load, and the dashed line marks the average that every node should end at. Nodes below the average are filled blue, nodes above it orange.

- **Graph** chooses the lecture example or another topology. **New random graph** builds a random connected graph.
- **α** chooses the global rule $1/(\Delta+1)$, the per-edge rule, the lecture's 0.5, or a custom value set with the slider.
- **Whole tasks only** rounds every flow down to an integer number of tasks.
- **Next step** and **Back** move through the iterations, **Play** runs them automatically, and **Reset** returns to the starting loads. The arrow keys and Space do the same.
- **Click a node** to see its next update worked out in full on the **This node** tab. **Drag** a node to move it.
- The **Loads** tab lists every load and the flow along each edge. Before the first step you can type new starting loads there.
- The **Convergence** tab plots $\sigma$, the standard deviation of the loads, on a log scale, with the $\sigma_0 \gamma^k$ prediction dashed.

Arrows and numbers on the edges show how much load moved along each edge in the last step.

---

## Interactive Tutorial

<a href="../embeds/diffusion-algorithm.html" target="_blank" rel="noopener" class="md-button">&#x2197; Open in new window</a>

<iframe src="../embeds/diffusion-algorithm.html" title="Interactive diffusion load balancing tutorial" class="tutorial-frame" width="100%" height="640" loading="lazy"></iframe>

---

## Key Observations

!!! example "Things to try"
    1. **Reproduce the lecture slides.** Set α to *0.5, as in the slides* and step four times. Compare the loads with the table above, then press **Play** and watch B swing between 4 and 9.
    2. **Apply the convergence condition.** Switch back to $1/(\Delta+1)$. Check that the total stays at 26. Click B and step: once B holds 6.5, its neighbours hold $26 - 6.5 = 19.5 = 3 \times 6.5$ between them, so B's three terms always cancel.
    3. **Find the stability limit.** Choose *Custom* α on the lecture graph and move the slider. $\gamma$ is smallest (0.6) at $\alpha = 0.4$ and reaches 1 at $\alpha = 0.5$. Above 0.5 the oscillation grows and loads go negative. $1/(\Delta+1) = 0.25$ is a safe choice for any graph with maximum degree 3, not the fastest one for this graph.
    4. **Compare topologies.** *Complete graph K5* balances in a single step ($\gamma = 0$). *Ring of 6* takes 18 steps and *Path of 5* takes 54. Fewer links between distant nodes means slower diffusion.
    5. **Global versus per-edge α.** On *Hub with a tail*, the hub's degree of 4 forces $\alpha = 1/5$ on every edge under the global rule. The per-edge rule lets the tail edges use $1/3$, reducing the steps needed from 123 to 98.
    6. **See the granularity problem.** Tick *Whole tasks only* on the lecture graph. The algorithm stops after three steps at $(10, 7, 5, 4)$.

---

## Limitations

- **Slow convergence.** The imbalance shrinks geometrically, but on sparse graphs $\gamma$ is close to 1 and many rounds are needed. Each round costs communication.
- **Granularity.** Tasks are indivisible, and migrating a task costs time and energy. A balanced fractional load may have no exact task-level implementation.
- **Balance is not schedulability.** Equal loads do not guarantee that every task meets its deadline. Check the resulting mapping against the timing constraints separately.

---

## Related Material

- [Mapping](../hw-sw-codesign/mapping.md) — dynamic mapping and load balancing
- [Design Space Exploration](../hw-sw-codesign/design-space-exploration.md)
