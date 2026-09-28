# Mapping

*Lecture 10 · Platform-based Design and Mapping*

## Overview

**Mapping** is the process of assigning partitioned tasks to specific resources on a target platform. While partitioning decides *what* goes to hardware vs software, mapping decides *where* on the platform each task executes.

In **platform-based design**, the target architecture is selected from a reusable platform or a parameterised library of components. The mapping step bridges the application model and this platform model. Map communication as well as computation: tasks need paths through memories, buses or networks, with a resource-sharing policy for each shared element.

The lecture distinguishes **allocation** (selecting resources), **binding** (assigning functions to them) and **scheduling** (deciding execution order and timing). These decisions interact and may need to be revisited together.

---

## Platform-Based Design

Platform-based design is a methodology where systems are built on top of pre-defined hardware platforms rather than designing custom hardware from scratch. This approach:

- **Reduces design time** by reusing validated platform components
- **Lowers cost** through economies of scale
- **Constrains the design space** to feasible implementations on the chosen platform

A platform typically consists of:

| Component | Examples |
|-----------|----------|
| **Processing elements (PEs)** | CPUs, DSPs, GPUs, FPGAs, custom accelerators |
| **Memory hierarchy** | Caches, scratchpads, shared memory, DMA |
| **Communication infrastructure** | Buses, crossbars, networks-on-chip (NoC) |
| **I/O interfaces** | Timers, interrupts, peripherals, sensors, actuators |

---

## The Mapping Problem

Given:

- A set of tasks $T = \{t_1, t_2, \ldots, t_n\}$ from the partitioning step
- A platform with resources $R = \{r_1, r_2, \ldots, r_m\}$

Find an assignment $M: T \rightarrow R$ that optimises objectives such as:

- **Minimise execution time** (meet real-time deadlines)
- **Minimise communication cost** (reduce data transfer overhead)
- **Balance resource utilisation** (avoid bottlenecks)
- **Minimise energy consumption**

subject to constraints:

- Resource capacity (memory, computation)
- Task compatibility (not all tasks can run on all PEs)
- Communication bandwidth
- Real-time deadlines

---

## Mapping vs Partitioning

| Aspect | Partitioning | Mapping |
|--------|-------------|---------|
| **Question** | HW or SW? | Which specific resource? |
| **Abstraction** | High-level (HW/SW domains) | Low-level (platform resources) |
| **Input** | Task graph | Partitioned tasks + platform model |
| **Output** | HW/SW assignment | Resource binding + schedule |
| **Constraints** | Communication cost, area, power | Deadlines, capacity, bandwidth |

!!! note
    In practice, partitioning and mapping are often interleaved or performed jointly, as mapping constraints can influence partitioning decisions.

---

## Mapping Techniques

### Static Mapping

Tasks are assigned to resources at design time. Suitable when the workload is predictable.

- **Exhaustive search**: Optimal but $O(m^n)$ complexity — only feasible for small problems
- **Heuristic approaches**: Greedy assignment, simulated annealing, genetic algorithms
- **Integer Linear Programming (ILP)**: Formulate as a constrained optimisation problem

### Dynamic Mapping

Tasks are assigned at runtime based on current system state. Used when workload varies.

- **Priority-based scheduling**: Assign tasks to available PEs based on priority
- **Load balancing**: Distribute tasks to equalise PE utilisation
- **Migration**: Move tasks between PEs in response to changing conditions

---

## Example: Mapping to a Heterogeneous Platform

Consider a platform with a CPU and an FPGA accelerator:

**Task Graph:**

```
T1 --> T2 --> T4
       ^      ^
       T3 ----+
```

**Platform:**

```
+-------------+-------------+
|     CPU     |    FPGA     |
|             |             |
|   Memory    |   Memory    |
+-------------+-------------+
|          Bus / NoC        |
+---------------------------+
```

After partitioning: T1, T2 → SW; T3, T4 → HW

Mapping decides:

- T1, T2 → CPU (execution order? scheduling?)
- T3 → FPGA accelerator block A
- T4 → FPGA accelerator block B
- Communication: T2→T4 data transfer via shared memory or DMA

The mapping must ensure that data dependencies are respected, communication latency is accounted for, and deadlines are met.

## Hungarian algorithm: an exact assignment model

For a one-to-one assignment of tasks to processing elements with independent costs, the Hungarian algorithm minimises the sum of selected costs. The lecture uses:

| Task | PE1 | PE2 | PE3 | PE4 |
|---|---:|---:|---:|---:|
| T1 | 80 | 40 | 50 | 46 |
| T2 | 40 | 70 | 20 | 25 |
| T3 | 30 | 10 | 20 | 30 |
| T4 | 35 | 20 | 25 | 30 |

Subtract each row minimum, then each column minimum. Search for independent zeros: one per row and column. If there are too few, cover all zeros with a minimum set of rows/columns, subtract the smallest uncovered value from uncovered entries and add it at intersections of covering lines. Repeat until a complete zero assignment is available.

An optimal assignment is T1→PE4, T2→PE3, T3→PE2, T4→PE1, costing $46+20+10+35=111$ in the original matrix. Choosing the cheapest entry separately in each row fails because several tasks want PE2.

This solves the stated assignment problem. Pairwise communication costs, several tasks sharing one PE, or deadline interference require a richer model; they cannot be added merely by calling the same matrix result optimal.

## Diffusion: redistribute load between neighbours

For node loads $l_i^{(k)}$ and symmetric link weights $\alpha_{ij}$, a synchronous update is

$$l_i^{(k+1)}=l_i^{(k)}+\sum_{j\in N(i)}\alpha_{ij}(l_j^{(k)}-l_i^{(k)}).$$

Compute every transfer from the old load vector before updating any node. Symmetric transfers preserve total load. For a fixed, connected, undirected graph, a conservative uniform weight is $\alpha=1/(\Delta+1)$, where $\Delta$ is the maximum degree. The lecture also gives the local choice $\alpha_{ij}=1/(1+\max(\deg i,\deg j))$.

For two connected nodes with loads 10 and 2 and weight $1/2$, one step produces 6 and 6. Real tasks are indivisible and migration has a cost, so a fractional load transfer may not have an exact task-level implementation. Balance alone also does not establish deadlines.

Use the [diffusion tutorial](../tutorials/diffusion-algorithm.md) to explore convergence, then compare a proposed redistribution against the task and communication constraints.

## Genetic mapping

A chromosome can encode the processor assigned to each task. Selection, crossover and mutation generate new assignments, while evaluation measures latency, energy or other objectives. Reject or penalise infeasible assignments explicitly; a good average fitness does not establish a hard deadline guarantee. The lecture contrasts simulation-based evaluation with analytical timing tests.

---

## Summary

| Concept | Description |
|---------|-------------|
| **Mapping** | Assigning tasks to specific platform resources |
| **Platform-based design** | Building on pre-defined hardware platforms |
| **Static mapping** | Fixed assignment at design time |
| **Dynamic mapping** | Runtime assignment based on system state |

---

## Related Material

- [HW/SW Co-Design Introduction](introduction.md)
- [Design Space Exploration](design-space-exploration.md)
- [Partitioning Algorithms](partitioning.md)
