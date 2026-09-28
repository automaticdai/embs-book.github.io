# Introduction to Embedded Systems Specification

*Lecture 4 · Embedded Systems Specification*

## What is a Specification?

A **specification** is a precise description of what a system must do and the constraints it must satisfy. It is the reference against which the design is later verified, so ambiguity or omissions here lead directly to faulty systems.

---

## Requirements

Requirements are usually divided into two kinds:

- **Functional requirements** — *what* the system does (e.g., "open the valve when pressure exceeds 5 bar")
- **Non-functional requirements** — *how well* it does it (e.g., timing, power, cost, reliability, safety)

!!! warning
    Natural-language requirements are easy to write but often ambiguous. Critical properties such as deadlines should be stated precisely and measurably.

---

## Models of Computation

A **model of computation (MoC)** defines how components of a system execute and communicate. Choosing a suitable MoC makes the specification easier to write, analyse, and implement.

| Model | Describes | Typical Use |
|-------|-----------|-------------|
| **Finite State Machines (FSMs)** | States and transitions triggered by events | Control logic, protocols |
| **Statecharts** | Hierarchical and concurrent FSMs | Complex reactive control |
| **Dataflow (e.g., SDF)** | Actors exchanging tokens over channels | Signal and media processing |
| **Petri Nets** | Concurrency, synchronisation, and shared resources | Manufacturing, distributed systems |
| **Discrete Event** | Timestamped events processed in order | Hardware simulation |

!!! info "Key Insight"
    No single model suits every system. Many designs combine models — for example, statecharts for control and dataflow for signal processing.

## Structure, behaviour and metamodels

A **static model** describes composition: components, interfaces and their relationships. A **dynamic model** describes execution: state changes, timing, concurrency and synchronisation. Both are needed to reason about a concurrent embedded application.

An application graph can annotate vertices with computation costs and deadlines, and edges with data volumes or latency requirements. A platform graph instead describes processors, memories and communication links. The same graph notation therefore needs an explicit interpretation.

A **metamodel** defines the allowed model constructs and their relationships. A particular model conforms to those rules and represents a particular system. Syntax tells us which descriptions are legal; behavioural semantics tells us what their execution means.

An FSM captures states and transitions. Statecharts add hierarchy through **OR states** (one active substate) and concurrency through **AND regions** (one active substate in each region). Define event handling and transition semantics when using these features.

## Turn requirements into checks

The lecture's specification checklist covers purpose and scope, functionality, performance, hardware and software interfaces, environmental conditions, applicable compliance requirements, and testing. Capture fixed platform constraints where they exist, while leaving genuine implementation choices open.

| Illustrative requirement | What must be made precise | Possible check |
|---|---|---|
| Process sensor samples promptly | Sampling interval and deadline measured from a defined event | Trace acquisition and completion timestamps |
| Fit in memory | Separate code, stack, heap and buffer budgets | Linker map and bounded buffer/stack analysis |
| Handle failed readings | Detection rule, output behaviour and recovery condition | Inject missing and out-of-range samples |
| Operate within an energy budget | Workload and operating conditions | Measure energy over the specified workload |

Include exceptional behaviour and measurable acceptance criteria. A requirement such as “respond quickly” cannot support a repeatable verification decision.

---

## Specification Languages and Tools

Models are expressed using languages and tools such as:

- **UML / SysML** — graphical modelling of structure and behaviour
- **Simulink / Stateflow** — dataflow and state machine modelling with simulation and code generation
- **SDL** — communicating state machines for protocols
- **SystemC** — C++ modelling constructs with an event-driven simulation kernel
- **Ptolemy II** — actors and directors for hierarchical models with explicit execution semantics
- **Hardware description languages** (VHDL, Verilog) — hardware behaviour and structure

---

## Properties of a Good Specification

A good specification is:

- **Unambiguous** — has exactly one interpretation
- **Complete** — covers all required behaviour, including error cases
- **Consistent** — contains no contradictory requirements
- **Verifiable** — every requirement can be checked by test or analysis
- **Clear about constraints** — separates required behaviour and fixed platform constraints from design choices

---

## Next Steps

- Build actor models in [Ptolemy II](ptolemy.md)
- Compare [continuous time, discrete event and dataflow semantics](models-of-computation.md)
- Calculate [SDF repetition vectors, schedules and buffer sizes](dataflow.md)
- Review the overall design process in [Embedded Systems Design](../embedded-systems-design/introduction.md)
- Continue to [HW/SW Co-Design](../hw-sw-codesign/introduction.md) to see how a specification is partitioned and mapped onto hardware and software
