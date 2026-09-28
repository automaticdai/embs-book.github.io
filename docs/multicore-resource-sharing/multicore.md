# Multicore Scheduling

*Lecture 17 · Multiprocessor Scheduling*

With several processors, a scheduler must decide both which job runs and where it runs. The examples below use sequential tasks: a job may migrate, but cannot execute on two cores simultaneously.

## Describe the platform

| Platform model | Assumption |
|---|---|
| Identical processors | Same execution capabilities and speeds |
| Uniform processors | Execution speeds differ by processor-specific factors |
| Unrelated heterogeneous processors | Task execution costs depend on the particular task–processor pair |

On $m$ identical unit-speed processors, $\sum_iU_i\le m$ is a necessary capacity condition for a sustained workload. Each sequential task must also fit its individual execution and deadline requirements. These conditions alone do not prove that a particular scheduler succeeds.

## Global, partitioned and semi-partitioned scheduling

| Approach | Assignment | Main analysis issue |
|---|---|---|
| Global | Ready jobs compete across cores and may migrate | Concurrent interference and migration cost |
| Partitioned | Each task is assigned permanently to one core | Find an assignment for which every core passes its scheduling test |
| Semi-partitioned | Most tasks stay fixed; selected tasks migrate | Guarantee each part's execution and the migration order |

Global scheduling can use capacity across processors, but incurs costs for migrations, caches and shared scheduling structures. Partitioning permits single-processor scheduling analysis once the assignment is fixed, provided shared-hardware and resource interference are also included.

## Why global EDF can fail

Use the lecture's two-processor example:

| Task | $C$ | $T$ | $D$ |
|---|---:|---:|---:|
| a | 3 | 10 | 10 |
| b | 3 | 10 | 10 |
| c | 8 | 12 | 10 |

All release at time 0. With an EDF tie-break that selects a and b, both cores execute them until time 3. Task c then has only seven time units until its deadline, but needs eight. Spare capacity on a second core cannot accelerate a sequential job.

A feasible first-deadline schedule does exist: run c on one core from 0 to 8 and run a then b on the other from 0 to 6. This illustrates why the single-processor EDF optimality result does not carry over. The **Dhall effect** describes much more extreme poor utilisation guarantees for global priority policies when very heavy tasks compete with short jobs.

## Partitioning is a packing problem

For independent implicit-deadline tasks under per-core EDF, an assignment passes if each core's utilisation is at most 1. For fixed priorities or shorter deadlines, run the appropriate schedulability test for each core; utilisation is not enough.

A first-fit heuristic orders tasks, then places each task on the first core where the enlarged task set remains schedulable. The ordering affects success. If a heuristic cannot place a task, another assignment may still work.

The lecture's tasks with $(C,T,D)=(9,10,10),(9,10,10),(2,10,10)$ have total utilisation 2. They cannot be fully partitioned onto two cores, because the two heavy tasks each leave capacity for only one time unit per period.

With zero migration overhead, a semi-partitioned schedule can run the third task for one unit on core 1 at $[0,1)$, then one unit on core 2 at $[1,2)$. Run the first heavy task on core 1 at $[1,10)$, and the second on core 2 at $[0,1)$ and $[2,10)$. Repeat every ten units. This is an illustrative schedule for the lecture task set; real migration and preemption costs must also fit.

## Shared resources across processors

A task spinning for a remote lock consumes its local processor. A suspended task releases that processor but introduces suspension and resumption behaviour. Either choice needs analysis of remote waiting as well as local interference.

FIFO request ordering helps prevent repeated overtaking, but preemption of a remote lock holder can still delay progress. Making all resource access non-preemptive bounds some delays at the cost of blocking unrelated urgent work. Nested resources require an additional deadlock argument, such as a common lock order.

**MSRP and MrsP are distinct protocols.** The non-preemptive FIFO spinning scheme described in part of the lecture is associated with MSRP. MrsP uses local priority ceilings and a helping mechanism: work for a pre-empted resource holder can continue on a waiting task's processor. Protocol rules and analysis must be used together; see the research paper on [MrsP analysis and helping](https://www.sciencedirect.com/science/article/pii/S0164121219302237).

## Account for shared hardware too

Even without software locks, shared caches, memory controllers and interconnects can increase execution times. A WCET measured in isolation may be unsuitable when other cores are busy. State which interference is included in execution costs and which is accounted for separately.

??? question "Check your understanding"
    Three implicit-deadline tasks each have utilisation 0.6. Can they be fully partitioned onto two cores?

    No. Total utilisation is only 1.8, but each core can hold at most one of these tasks. A task migration scheme needs a separate feasibility and overhead argument.

See [mapping](../hw-sw-codesign/mapping.md) for assignment algorithms, or continue to [mixed-criticality systems](mixed-criticality.md).
