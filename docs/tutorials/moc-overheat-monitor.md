# One Overheat Monitor, Three Models of Computation

This interactive tutorial runs one small system under three different **models of computation**: continuous time (CT), discrete event (DE) and dataflow. The system stays the same; only the execution rules change.

A board heats up. A sensor samples its temperature every 100 ms, the last four samples are averaged, and an LED turns on when the average exceeds 80 °C.

!!! info "What you'll learn"
    - Why a numerical solver only approximates a continuous-time signal, and how the step size affects accuracy and stability
    - How a discrete-event director orders events by timestamp, microstep and depth, and why model time jumps between events
    - How dataflow actors fire on token availability alone, and why any valid firing order produces the same output (Kahn determinism)
    - Where SDF production and consumption rates come from

---

## How to Use

**Continuous time:** drag the **solver step h** slider and press **Play**. Compare the Euler points with the exact curve, and the time at which each first reports the alarm.

**Discrete event:** press **Process next event** repeatedly. Watch the global event queue, the model-time clock and the depth badges (`d0`–`d3`) on each actor.

**Dataflow:** click any actor with a green outline to fire it, in any order you choose. **Fire one by a valid schedule** picks a firing for you.

Keyboard: arrow keys switch tabs; Space plays, steps or fires depending on the tab.

---

## Interactive Tutorial

<a href="../embeds/moc-overheat-monitor.html" target="_blank" rel="noopener" class="md-button">&#x2197; Open in new window</a>

<iframe src="../embeds/moc-overheat-monitor.html" title="Interactive overheat monitor under continuous-time, discrete-event and dataflow models" class="tutorial-frame" width="100%" height="1000" loading="lazy"></iframe>

---

## Key Observations

!!! example "Things to try"
    1. **CT:** with $h=60$ s the solver reports the alarm at 180 s, about 5 s before the true crossing at 184.9 s. Increase $h$ beyond $\tau=120$ s to see oscillation, and beyond $2\tau=240$ s to see the solver diverge.
    2. **DE:** at 0 ms, the Display waits until the Averager has produced its output, even though both received the sample at the same timestamp. Note that model time then jumps straight to 100 ms.
    3. **DE:** the average first exceeds 80 °C at 400 ms (83.75 °C) and drops back at 600 ms (77.5 °C).
    4. **Dataflow:** fire the Sensor several times before anything else, then reset and interleave the actors differently. The LED always sees `off, on, off`.
    5. **Dataflow:** compare the averager here (non-overlapping blocks of four) with the DE sliding window. They are different actors, so they compute different results.

---

## Related Material

- [Models of Computation](../specification/models-of-computation.md)
- [KPN and Synchronous Dataflow](../specification/dataflow.md)
- [Ptolemy II](../specification/ptolemy.md)
