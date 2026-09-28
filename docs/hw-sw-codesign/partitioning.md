# HW/SW Partitioning

*Lecture 9 · Co-design and Partitioning*

## Overview

**Partitioning** is the process of dividing system functionality between hardware and software components. The goal is to find an assignment that optimises a given objective (e.g., minimise communication cost) while satisfying design constraints.

---

## Problem Formulation

The partitioning problem can be modelled as a **graph partitioning** problem:

- **Nodes** represent tasks or functional blocks
- **Edges** represent communication or data dependencies between tasks
- **Edge weights** represent the communication cost
- **Partitions** represent HW and SW assignments

One objective is to **minimise the cut cost** — the total weight of edges crossing the partition boundary (i.e., communication between HW and SW). Without balance or capacity constraints, assigning everything to one partition gives a zero cut, so the constraints are essential. General partitioning can use more than two partitions and objectives beyond communication.

## Integer linear programming

The lecture defines binary variables $x_{i,k}$ indicating whether object i is assigned to partition k. Each object belongs to exactly one partition:

$$\sum_{k=1}^{m}x_{i,k}=1,\qquad x_{i,k}\in\{0,1\}.$$

With assignment cost $c_{i,k}$, minimise

$$\sum_{i=1}^{n}\sum_{k=1}^{m}c_{i,k}x_{i,k}.$$

A partition capacity can be expressed as $\sum_i a_{i,k}x_{i,k}\le H_k$, where $a_{i,k}$ is object i's resource requirement in partition k. Setting all $a_{i,k}=1$ limits the number of objects instead.

This assignment-cost objective does not itself represent pairwise cut costs. For two partitions, use $x_i\in\{0,1\}$ and a binary cut variable $z_{ij}$ for each nonnegative edge cost $w_{ij}$. Constraints $z_{ij}\ge x_i-x_j$ and $z_{ij}\ge x_j-x_i$, together with minimising $\sum w_{ij}z_{ij}$, charge for edges with endpoints on opposite sides. Add the relevant capacity or balance constraints.

An exact solver can establish optimality only if it completes with the appropriate proof. A feasible solution returned at a time limit need not be optimal.

---

## Partitioning Algorithms

### Exact Methods

- **Exhaustive search**: Try all possible partitions. Guarantees optimality but has exponential complexity — $O(2^n)$ for $n$ nodes.
- **Integer Linear Programming (ILP)**: Formulate as an optimisation problem. Optimal but computationally expensive for large problems.

### Heuristic Methods

- **Constructive methods** build an assignment from an empty solution, for example by grouping strongly connected tasks through hierarchical clustering.
- **Iterative methods** transform a complete assignment, often starting from a constructive solution.
- **Greedy algorithms**: Make locally optimal choices at each step
- **Kernighan-Lin (KL) algorithm**: Iteratively swap pairs of nodes to reduce cut cost
- **Simulated annealing**: Probabilistic method that can escape local optima
- **Genetic algorithms**: Evolution-based search over the partition space

---

## The Kernighan-Lin Algorithm

The **Kernighan-Lin (KL) algorithm** is a classic heuristic for graph bi-partitioning. Its balanced form starts with two equally sized partitions and swaps pairs of nodes, preserving the number of nodes in each partition. Equal node counts do not ensure equal hardware area or execution load when node costs differ.

### Key Concepts

**D-value**: For each node $v$, the D-value measures how much the node "wants" to move:

$$D(v) = E(v) - I(v)$$

where:

- $E(v)$ = **external cost** — total weight of edges to nodes in the *other* partition
- $I(v)$ = **internal cost** — total weight of edges to nodes in the *same* partition

A positive D-value means the node has more connections to the other partition — it may benefit from being moved.

### Swap Gain

The gain from swapping nodes $a$ (in SW) and $b$ (in HW) is:

$$g(a, b) = D(a) + D(b) - 2 \cdot c(a, b)$$

where $c(a, b)$ is the edge weight between $a$ and $b$ (0 if no direct edge).

### Algorithm Steps

1. Compute D-values for all unlocked nodes
2. Find the pair $(a, b)$ with maximum gain $g(a, b)$
3. Lock $a$ and $b$ (they cannot be selected again in this pass)
4. Record the gain; tentatively swap $a$ and $b$
5. Repeat until all nodes are locked
6. Find the prefix of swaps with maximum cumulative gain
7. If the maximum cumulative gain > 0, apply those swaps and start a new pass
8. If no improvement is possible, the algorithm has **converged**

Individual tentative gains may be negative. Keeping the best cumulative prefix lets the algorithm reach improvements that an immediately improving pair-swap heuristic would miss.

!!! example "Lecture swap calculation"
    With $D(a)=1$, $D(b)=-2$ and $c(a,b)=1$, the gain is $1-2-2=-3$. The cut increases by 3, from 3 to 6. The edge between a and b remains a cut edge after both move, which is why its double-counted contribution must be subtracted.

!!! tip "Interactive Tutorial"
    Try the [**KL Algorithm Interactive Tutorial**](../tutorials/kl-algorithm.md) to step through this algorithm visually and build your intuition!

---

## Complexity

- The straightforward implementation discussed in the lecture takes $O(n^3)$ per pass: it searches $O(n^2)$ pairs at each of $O(n)$ selections. More efficient variants use different data structures and selection procedures.
- Typically converges in a small number of passes
- Does **not** guarantee a global optimum — result depends on the initial partition

---

## Summary

| Method | Optimality | Complexity | Practical Use |
|--------|-----------|------------|---------------|
| Exhaustive | Global | $O(2^n)$ | Small problems only |
| KL Algorithm | No global guarantee | $O(n^3)$ per pass for the straightforward version | Balanced two-way partitions |
| Simulated Annealing | No finite-run global guarantee | Depends on search budget | Broader search with probabilistic moves |
| Genetic Algorithm | No global guarantee | Depends on population and evaluations | Searching alternative assignments |
