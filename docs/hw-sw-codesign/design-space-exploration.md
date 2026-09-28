# Design Space Exploration

*Lecture 8 · System Level Design*

## Overview

**Design space exploration (DSE)** is the systematic process of evaluating alternative design choices to find solutions that best meet a set of objectives and constraints. In HW/SW co-design, the design space encompasses all possible combinations of partitioning, mapping, scheduling, and implementation decisions.

DSE is essential because embedded system design involves navigating trade-offs between competing objectives — no single design is typically best on all metrics.

---

## The Design Space

The design space for an embedded system includes choices at multiple levels:

| Level | Design Choices |
|-------|---------------|
| **Architecture** | Platform selection, number and type of processing elements |
| **Partitioning** | Which tasks are implemented in HW vs SW |
| **Mapping** | Which tasks are assigned to which resources |
| **Scheduling** | In what order tasks execute, priority assignment |
| **Implementation** | Algorithm variants, data representations, communication protocols |

The total number of candidate designs grows combinatorially with the number of choices at each level, making exhaustive evaluation infeasible for all but the smallest systems.

---

## Objectives and Constraints

DSE aims to optimise one or more **objectives** while satisfying **constraints**:

**Common objectives:**

- Minimise execution time / latency
- Minimise energy or power consumption
- Minimise hardware area or cost
- Maximise throughput

**Common constraints:**

- Real-time deadlines must be met
- Memory capacity must not be exceeded
- Power budget must not be exceeded
- Hardware area must fit the target device

!!! note
    Objectives often conflict — for example, reducing latency may require more hardware resources (higher cost), while minimising cost may increase execution time.

---

## Multi-Objective Optimisation

When multiple objectives are in play, there is generally no single "best" design. Instead, DSE identifies the set of **Pareto-optimal** solutions.

A design $A$ **dominates** design $B$ if $A$ is at least as good as $B$ on all objectives and strictly better on at least one. The **Pareto front** is the set of all non-dominated designs.

```
Cost
 ^
 |  x
 |    x         x = candidate designs
 |      x       o = Pareto-optimal designs
 |   o    x
 |     o
 |       o
 |         o
 +-----------------> Latency
```

The designer then selects from the Pareto front based on application priorities and constraints. A candidate non-dominated within a sampled set is not necessarily Pareto-optimal in the entire design space.

## Worked example: choose among feasible designs

Suppose both latency and energy should be minimised. These are illustrative measurements for the same operation:

| Candidate | Latency (ms) | Energy (mJ) | Status among these candidates |
|---|---:|---:|---|
| A | 8 | 4 | Non-dominated |
| B | 6 | 5 | Non-dominated |
| C | 9 | 6 | Dominated by A and B |
| D | 5 | 8 | Non-dominated |

A, B and D represent different trade-offs. If latency must be at most 7 ms and energy at most 6 mJ, only B is feasible. A good energy result does not compensate for violating a hard deadline.

Lecture 8 distinguishes the **solution space** $X$ (choices such as processor count or bus width) from the **objective space** $F$ (measured or estimated outcomes). Evaluation maps $x\in X$ to $f(x)\in F$. Record the model and assumptions behind that evaluation; refining the model may change which designs remain attractive.

---

## DSE Methods

### Exhaustive Enumeration

Evaluate every possible design. Guarantees finding the optimal solution but only feasible for very small design spaces.

- **Complexity**: Product of choices at each level (often exponential)
- **Use case**: Small problems, validation of heuristic results

### Heuristic Search

Use guided search strategies to explore the design space efficiently:

- **Simulated annealing**: Random perturbations with a cooling schedule that gradually reduces the acceptance of worse solutions
- **Genetic algorithms**: Population-based search using selection, crossover, and mutation operators
- **Tabu search**: Local search with memory to avoid revisiting recently explored designs

### Analytical Models

Use mathematical models to estimate performance without full simulation:

- **Worst-case execution time (WCET) analysis** for timing
- **Energy models** based on activity factors and switching capacitance
- **Queuing models** for communication and resource contention

### Simulation-Based Evaluation

Run detailed simulations of candidate designs:

- Cycle-accurate simulation for timing accuracy
- Power simulators for energy estimation
- Co-simulation of HW and SW components together

!!! tip
    In practice, DSE often combines fast analytical models for initial pruning with detailed simulation for evaluating the most promising candidates.

---

## DSE Flow

A typical design space exploration flow:

1. **Define the design space** — identify all design choices and their ranges
2. **Define objectives and constraints** — what to optimise, what must be satisfied
3. **Sample candidate designs** — exhaustive, random, or guided by a search algorithm
4. **Evaluate each candidate** — using models, simulation, or estimation
5. **Identify Pareto-optimal solutions** — filter dominated designs
6. **Select the final design** — based on application priorities

---

## Summary

| Concept | Description |
|---------|-------------|
| **Design space** | All possible combinations of design choices |
| **Pareto optimality** | No design is better on all objectives simultaneously |
| **Pareto front** | The set of non-dominated designs |
| **Exhaustive search** | Optimal but exponential complexity |
| **Heuristic search** | Efficient but no optimality guarantee |
| **Analytical models** | Fast estimation for pruning the space |
| **Simulation** | Detailed evaluation of promising candidates |

---

## Related Material

- [HW/SW Co-Design Introduction](introduction.md)
- [Partitioning Algorithms](partitioning.md)
- [Mapping](mapping.md)
