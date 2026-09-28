# Resource Sharing, Multicore and Mixed Criticality

*Lectures 16–18 · Extending the real-time task model*

## Overview

Real-time tasks rarely run in isolation. They share **resources** — data structures, peripherals, buses — and may run on **multicore** processors or provide services at different **criticality levels**. These extensions change the assumptions behind the basic timing tests.

| Chapter | Main question |
|---|---|
| [Resource sharing](resource-sharing.md) | How much can a lower-priority lock holder delay a task? |
| [Multicore scheduling](multicore.md) | Which core runs each job, and how does interference affect timing? |
| [Mixed-criticality systems](mixed-criticality.md) | Which guarantees remain when execution exceeds a lower-assurance budget? |

---

## Resource Sharing and Priority Inversion

Shared resources are protected by mutual exclusion, so a task may have to wait for a lower-priority task to release a resource. This is **blocking**.

**Priority inversion** occurs when a high-priority task is delayed by lower-priority work, for example a task holding a required resource. Medium-priority tasks can further delay the holder. Without suitable arrival bounds or a resource protocol, the delay need not be bounded by the critical section's length.

!!! example "Mars Pathfinder (1997)"
    The Mars Pathfinder lander suffered repeated system resets caused by priority inversion. The problem was fixed remotely by enabling priority inheritance on the affected mutex.

---

## Resource Access Protocols

| Protocol | Idea | Properties |
|----------|------|------------|
| **Priority Inheritance (PIP)** | A task holding a resource inherits the priority of the highest task it blocks | Bounds inversion, but chained blocking and deadlock are possible |
| **Priority Ceiling (PCP)** | Each resource has a ceiling equal to the highest priority of its users; locking is restricted by ceilings | Blocked at most once, deadlock-free |
| **Immediate Ceiling (ICPP)** | A task's priority is raised to the resource ceiling as soon as it locks it | Same worst-case bound as PCP, simpler to implement |
| **Stack Resource Policy (SRP)** | Pre-emption levels control when a task may start | Works with EDF, allows shared stacks |

These properties require the protocol's single-processor assumptions and locking rules. The [resource-sharing chapter](resource-sharing.md) derives a ceiling blocking bound and explains why it differs from PIP.

With a blocking term $B_i$, response-time analysis becomes:

$$R_i = C_i + B_i + \sum_{j \in hp(i)} \left\lceil \frac{R_i}{T_j} \right\rceil C_j$$

---

## Multicore Scheduling

On multicore processors, tasks can be scheduled in two main ways:

| Approach | Description | Trade-offs |
|----------|-------------|------------|
| **Partitioned** | Each task is statically assigned to one core; each core is scheduled independently | Reuses single-core analysis; assignment is a bin-packing problem |
| **Global** | Tasks share a single ready queue and may migrate between cores | Better load balancing; migration overheads and harder analysis |
| **Semi-partitioned** | Most tasks are partitioned; a few are split across cores | Combines the benefits of both, at the cost of complexity |

!!! warning
    Single-core results do not carry over directly. For example, with global EDF or global RM, a task set with total utilisation only slightly above 1 can miss deadlines, no matter how many cores are available (the **Dhall effect**).

---

## Shared Hardware Resources

Cores on the same chip also contend for **shared caches, memory buses, and interconnects**. This interference can greatly increase execution times and must be bounded — for example through cache partitioning, memory bandwidth regulation, or interference-aware analysis.

!!! tip "Interactive Tutorial"
    Try the [**Diffusion Load Balancing Tutorial**](../tutorials/diffusion-algorithm.md) to see how load can be balanced across processing nodes.

---

## Next Steps

- Follow the [multicore worked examples](multicore.md) for the limits of global scheduling and partitioning
- Explore [mixed-criticality budgets and mode changes](mixed-criticality.md)
- Review single-core schedulability tests in [Real-Time Scheduling](../real-time-scheduling/introduction.md)
- See how tasks are assigned to processing elements in [Mapping](../hw-sw-codesign/mapping.md)
