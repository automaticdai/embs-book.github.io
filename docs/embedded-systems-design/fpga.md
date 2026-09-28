# Design and Implementation on FPGAs

*Lecture 2 · Ian Gray — Design and Implementation on FPGAs*

An FPGA is a configurable digital circuit. Its configuration determines the logic and connections that implement an application. This gives the designer control over parallelism, arithmetic and memory access, alongside the software running on a processor.

## From software to custom hardware

| Implementation | What the designer changes | Main trade-off |
|---|---|---|
| Processor software | Instructions executed on an existing architecture | Flexible development; execution shares processor resources |
| FPGA | Logic and routing configured by a bitstream | Custom datapaths; finite logic, memory and arithmetic resources |
| ASIC | Circuit manufactured for the application | Specialisation and potential efficiency; fabrication cost and limited flexibility |

Acceleration depends on the workload. A parallel filter may benefit from several arithmetic units operating at once; a small operation dominated by data transfers may be faster in software. Include communication and setup time when comparing implementations.

## What is inside an FPGA?

**Lookup tables and registers** implement combinational and sequential logic. **Programmable interconnect** joins these into circuits. **Block RAM** provides local storage, and **DSP blocks** support arithmetic such as multiplication and accumulation. Configuration memory selects the circuit that these resources implement.

Resources are separate budgets. A design can run out of block RAM while using only a small fraction of the available logic. Placement and routing also affect whether the circuit meets its clock constraint.

## The implementation flow

| Stage | Output | Question to answer |
|---|---|---|
| Describe and simulate | HDL design and testbench results | Does the circuit implement the intended behaviour? |
| Synthesise | Netlist mapped to device resources | What logic, registers, memories and DSP blocks are needed? |
| Place and route | Physical placement and connections | Can the implementation meet timing and resource constraints? |
| Generate and load | Configuration bitstream | Does the configured device behave correctly? |
| Measure | Timing, throughput and resource evidence | Did the design decision improve the complete application? |

HDL describes concurrent hardware. A loop in a hardware description may create several copies of a circuit, depending on how it is written and synthesised. Always inspect the implementation reports.

## A processor and FPGA together

The lecture's Zynq platform combines ARM processing with programmable logic. A project therefore has two connected parts: processor software and an FPGA hardware design. The software configures accelerators, supplies data and handles results; the hardware performs selected operations.

For a filter accelerator, define the interface before building the datapath: input format, buffer addresses, transfer length, completion signalling and error behaviour. The processor and accelerator must agree on who owns each buffer and when its contents are valid.

!!! example "Count the complete cost"
    Suppose a software filter takes 100 µs. An accelerator takes 20 µs, but input transfer, setup and output transfer take 25 µs each. The complete accelerated operation takes 95 µs: only about a 1.05× speedup. Reducing transfers or processing a larger batch may matter more than speeding up the datapath further. These values are an illustrative example.

## Connect the practical to the design process

Keep a record of the software baseline, the function moved into hardware, the interface, the resource report and the measured result. This makes the final metrics traceable to the original design decision.

Use the [practical exercises](https://iangray001.github.io/embs/docs/practicals/) for the board setup and tool instructions. Continue with [HW/SW co-design](../hw-sw-codesign/introduction.md) to decide which functions belong in each implementation.

??? question "Check your understanding"
    Why might an accelerator with lower computation time fail to improve application latency?

    Transfers, setup, synchronisation and waiting for shared resources can outweigh the saved computation. Measure the full path from input availability to output availability.
