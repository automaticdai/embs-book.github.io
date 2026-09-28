# Embedded Software Design

*Lecture 11 · Embedded Software Design*

After [partitioning and mapping](../hw-sw-codesign/mapping.md), software components must become executable code on the selected processors. The design must account for the toolchain, execution time and the cost of sharing resources.

## From source to executable

| Step | Purpose |
|---|---|
| Compile | Translate source into target instructions or assembly |
| Assemble | Encode assembly as object code |
| Link | Resolve symbols and combine object files and libraries |
| Locate | Assign code and data to the target memory map, often through a linker script |
| Load and execute | Start the program on the target and observe its behaviour |

A **cross-compiler** runs on a development host and generates code for a different target. The executable must match the target instruction set, calling conventions, startup code and memory layout. A successful host build does not establish these properties.

An alternative is an intermediate representation executed by a virtual machine. A process VM supports an application runtime; a system VM presents a virtual machine to a guest operating system. Interpretation, compilation at runtime and runtime services introduce costs that must be included in the timing and memory model.

## Execution time and response time

**Execution time** measures processor time used by a job. **Response time** measures elapsed time from release to completion. Waiting for the processor or a resource increases response time even when the job's own execution time is unchanged.

For the single-processor fixed-priority model introduced later:

$$R_i=C_i+B_i+I_i,$$

where $C_i$ is WCET, $B_i$ is bounded blocking and $I_i$ is higher-priority interference. Other models can require additional terms.

Caches, pipelines, branch prediction, input-dependent paths and memory contention make execution time vary. A maximum observed in a test is evidence about those executions; it is not automatically a safe bound for all possible executions. Record the assumptions used to obtain each WCET, including the processor, compiler settings, memory behaviour and input bounds.

!!! example "A short computation can have a long response"
    A job released at 0 runs for 2 ms, is pre-empted for 4 ms, then runs for another 1 ms. Its execution time is 3 ms and its response time is 7 ms. A 5 ms deadline is missed despite the small execution cost.

## Choosing an execution structure

| Structure | How work starts | Design concern |
|---|---|---|
| Cyclic executive | A predetermined sequence, often driven by a timer | Fit all work and dependencies into frames |
| Non-preemptive event loop | Events select handlers that run to completion | A long handler delays all other handlers |
| Preemptive tasks | A scheduler chooses among ready tasks | Include interference, context switches and shared-resource blocking |

An event queue needs a capacity argument based on arrival bursts and service rates. Splitting a long handler improves responsiveness, but its state must survive between pieces. A preemptive RTOS provides execution contexts and scheduling services at an additional memory and timing cost.

## Processor customisation and retargeting

Profiling can identify frequently executed kernels suitable for a coprocessor or a custom instruction. An application-specific instruction-set processor changes the processor's instruction capabilities to suit a workload.

For a candidate operation group, compare the saved execution time with added hardware area, interface cost and compiler support. Extending an instruction set also requires a way for the compiler to emit the new instruction. A **retargetable compiler** is designed to support changes to the target architecture description.

This closes the design loop: a missed deadline may require a scheduling change, a code improvement or a new hardware/software partition. Continue with [RTOS mechanisms](introduction.md) and [fixed-priority analysis](../real-time-scheduling/fixed-priority.md).

??? question "Check your understanding"
    Why can a compiler optimisation improve average execution time while leaving the timing guarantee unresolved?

    It may speed up common paths without bounding the worst path or the effects of caches and shared memory. Re-establish the WCET assumptions for the resulting binary and target.
