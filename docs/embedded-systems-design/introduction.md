# Introduction to Embedded Systems Design

*Lectures 1 and 3 · Introduction to Embedded Systems; Embedded Systems Design*

## What is an Embedded System?

An **embedded system** is a computer system built into a larger product to perform a dedicated function. Unlike a general-purpose computer, it is designed around a specific application — a car's braking controller, a pacemaker, a router, or a washing machine.

---

## Characteristics

Embedded systems typically share several characteristics:

- **Dedicated function** — designed for one application, not for arbitrary software
- **Reactive** — continuously respond to inputs from sensors and the environment
- **Timing constraints** — many applications must produce correct results within deadlines
- **Resource-constrained** — limited processing power, memory, energy, and cost budget
- **Dependable** — often safety- or mission-critical, requiring high reliability

!!! example "Examples"
    Engine control units, flight controllers, medical infusion pumps, industrial PLCs, smart meters, and wireless sensor nodes are all embedded systems.

---

## Design Metrics

Embedded system design balances competing metrics:

| Metric | Description |
|--------|-------------|
| **Performance** | Latency and throughput of the system |
| **Power / Energy** | Consumption, battery life, and heat dissipation |
| **Size** | Physical dimensions and silicon area |
| **Unit Cost** | Cost to manufacture each unit |
| **NRE Cost** | One-off non-recurring engineering cost to design the system |
| **Time-to-Market** | Time taken to develop and release the product |
| **Flexibility** | Ability to change functionality after deployment |
| **Dependability** | Reliability, safety, and security |

!!! note
    Metrics often conflict. Custom hardware may improve performance or energy use for a suitable workload, while increasing development effort. Measure the complete system, including communication and interfaces.

---

## The Design Process

A typical embedded system design process moves through increasing levels of detail:

1. **Requirements** — Capture what the system must do and the constraints it must meet
2. **Specification** — Describe the required behaviour precisely, often with formal models
3. **Architecture** — Choose the hardware platform and system structure
4. **Components** — Design the hardware and software components
5. **Integration** — Combine the components into a working system
6. **Verification and Validation** — Check that the system meets its specification and requirements

!!! info "Key Insight"
    Decisions made early in the process — during specification and architecture — have the largest effect on cost and performance, and are the most expensive to change later.

---

## Levels of Abstraction

Designers work at different levels of abstraction as the design is refined:

| Level | Hardware View | Software View |
|-------|---------------|---------------|
| **System** | Processing elements, buses, memories | Tasks and communication |
| **Architecture** | Instruction set, microarchitecture | Algorithms and data structures |
| **Implementation** | Register-transfer level, gates | Source code, machine code |

Higher levels allow faster exploration of design alternatives; lower levels support more detailed estimates of timing, area, and power. Accuracy still depends on the validity of the model and its assumptions.

## Refinement and evidence

Lecture 3 follows a design from conceptual models through source and instruction models, transaction-level communication, cycle-accurate RTL, gates and physical layout. Each refinement adds implementation detail and reduces the range of choices still available.

Choose the least detailed model that can answer the current question. A task graph may be enough to compare partitions; a bus contention question needs a communication model; a clock constraint requires timing information from the hardware implementation.

| Activity | Question | Example evidence |
|---|---|---|
| Verification | Does the design satisfy its specification? | Simulation results, assertions, timing analysis |
| Validation | Does the system serve the intended application? | Tests with representative users or environmental conditions |
| Evaluation | How well does this candidate perform? | Latency, energy, memory and resource measurements |

Keep a trace from each requirement to its model, design choice and final measurement. For example, a sampling deadline should connect to the task period, processor mapping, response-time calculation and observed timing on the prototype. A fast component alone does not establish the full input-to-output deadline.

---

## Next Steps

- Connect the theory to [FPGA design and implementation](fpga.md)
- Learn how to describe system behaviour precisely in [Embedded Systems Specification](../specification/introduction.md)
- See how design decisions are split between hardware and software in [HW/SW Co-Design](../hw-sw-codesign/introduction.md)
