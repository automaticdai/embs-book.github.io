# Introduction to HW/SW Co-Design

*Lectures 8–9 · System Level Design; Co-design and Partitioning*

## What is HW/SW Co-Design?

**Hardware/Software Co-Design** is a methodology for designing embedded systems where hardware and software components are developed concurrently, with the goal of optimising system performance, cost, and power consumption.

---

## The Design Challenge

In embedded systems, functionality can be implemented in either:

- **Hardware (HW)**: Custom logic circuits, FPGAs, ASICs
- **Software (SW)**: Programs running on processors/microcontrollers

Each approach has distinct trade-offs:

| Aspect | Hardware | Software |
|--------|----------|----------|
| **Performance** | Custom parallel datapaths for selected operations | Depends on processor architecture, code and workload |
| **Flexibility** | ASIC logic is fixed; FPGA logic can be reconfigured | Software can be replaced within platform constraints |
| **Development cost** | Hardware design, verification and integration effort | Software development, validation and toolchain effort |
| **Unit cost** | Depends on technology, volume and device resources | Depends on the processor, memory and other platform needs |
| **Energy** | Specialisation can reduce energy per operation | Depends on execution time and processor power states |
| **Time to market** | Influenced by synthesis, verification and fabrication | Influenced by reuse, integration and software complexity |

These are design considerations, not universal rankings. A hardware accelerator that saves computation can still lose overall performance through transfers and synchronisation. Compare the complete implementation against the same requirements.

---

## Co-Design Flow

The typical HW/SW co-design flow involves:

1. **System Specification** — Define requirements and constraints
2. **Modelling** — Create a system-level model of behaviour
3. **Partitioning** — Decide which functions go to HW vs SW
4. **Synthesis** — Generate HW and SW implementations
5. **Co-Simulation** — Verify the combined system
6. **Integration** — Bring HW and SW together on the target platform

!!! info "Key Insight"
    The **partitioning** step is critical — it determines the fundamental architecture of the system and has the greatest impact on meeting design constraints.

---

## Why Co-Design Matters

Traditional approaches design hardware and software separately, leading to:

- Sub-optimal partitioning decisions
- Integration problems discovered late
- Missed performance or cost targets

Co-design addresses these issues by considering hardware and software as a unified design space from the start.

---

## Next Steps

- Learn about [Partitioning](partitioning.md) strategies and algorithms
- Try the [KL Algorithm Interactive Tutorial](../tutorials/kl-algorithm.md) to explore graph-based partitioning
