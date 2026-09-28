# Lecture Guide

Use this guide to find the book chapters that accompany each lecture. Lecture numbers follow the supplied teaching material; they are not a weekly timetable. Read the chapters alongside the slides and practical exercises.

## System specification and design

| Lecture | Topic | Read and practise |
|---|---|---|
| 0 | Module Introduction and Overview | [Module overview](overview.md) and learning outcomes |
| 1 | Introduction to Embedded Systems | [Characteristics, constraints and design metrics](../embedded-systems-design/introduction.md) |
| 2 | Design and Implementation on FPGAs | [FPGA resources, implementation flow and processor integration](../embedded-systems-design/fpga.md) |
| 3 | Embedded Systems Design | [Abstraction, refinement and design evidence](../embedded-systems-design/introduction.md#refinement-and-evidence) |
| 4 | Embedded Systems Specification | [Requirements, structural and behavioural models](../specification/introduction.md) |
| 5 | Embedded Systems Specification II | [Ptolemy actors, directors and lifecycle](../specification/ptolemy.md) |
| 6 | Computation Models | [Continuous time, discrete events and untimed models](../specification/models-of-computation.md) |
| 7 | Dataflow Models | [KPN, SDF balance equations, schedules and buffers](../specification/dataflow.md) |
| 8 | System Level Design | [Design space exploration and Pareto dominance](../hw-sw-codesign/design-space-exploration.md) |
| 9 | Co-design and Partitioning | [Co-design](../hw-sw-codesign/introduction.md), [ILP and KL partitioning](../hw-sw-codesign/partitioning.md), then the [KL tutorial](../tutorials/kl-algorithm.md) |
| 10 | Platform-based Design and Mapping | [Hungarian assignment, diffusion and genetic mapping](../hw-sw-codesign/mapping.md), then the [diffusion tutorial](../tutorials/diffusion-algorithm.md) |

## Software and real-time analysis

| Lecture | Topic | Read and practise |
|---|---|---|
| 11 | Embedded Software Design | [Compilation, WCET, execution structures and customisation](../rtos/embedded-software.md) |
| 12 | Real-Time Operating Systems | [RTOS mechanisms](../rtos/introduction.md) and [FreeRTOS tasks, communication and memory](../rtos/freertos.md) |
| 13 | Real-Time Systems | [Task models, notation and schedulability tests](../real-time-scheduling/introduction.md) |
| 14 | FPS | [Priority assignment and response-time analysis](../real-time-scheduling/fixed-priority.md) |
| 15 | EDF | [Processor demand, PDA and QPA](../real-time-scheduling/edf.md), then the [PDA/QPA explorer](../tutorials/pda-qpa.md) |
| 16 | Resource Sharing | [Priority inversion, inheritance and ceiling protocols](../multicore-resource-sharing/resource-sharing.md) |
| 17 | Multicore Scheduling | [Global, partitioned and semi-partitioned scheduling](../multicore-resource-sharing/multicore.md) |
| 18 | Mixed-criticality Systems | [Execution budgets, mode changes and response-time bounds](../multicore-resource-sharing/mixed-criticality.md) |

## Revision routes

**For modelling and design:** write a measurable requirement, select a model of computation, derive an SDF schedule, compare feasible design candidates, then explain a partition and mapping decision.

**For timing analysis:** identify the task model and assumptions, assign priorities, choose the correct test, calculate the result and state what it proves. Add blocking, multicore interference or mode changes only with an analysis that supports them.

**For implementation:** follow the [practical exercises](https://iangray001.github.io/embs/docs/practicals/), establish a baseline and connect each hardware/software change to measured timing and resource use.

## Using the lecture material

Lecture references at the start of chapters identify their teaching context. Worked examples are identified as lecture examples or illustrative examples. Self-check questions include expandable answers so you can attempt the reasoning first.

The book clarifies assumptions and terminology where needed, and links to primary documentation for implementation details. Assessment weights, deadlines, lab arrangements and tool setup should be checked through the official module information and VLE, since these can differ between editions of the teaching material.
