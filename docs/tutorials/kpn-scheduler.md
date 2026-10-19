# Scheduling a Kahn Process Network

This interactive tutorial lets you act as the scheduler for the **Kahn process network** from the dataflow lecture. You decide which process fires next; the network decides whether it can make progress.

Process `f` reads an input stream and sends even numbers up on channel `v` and odd numbers down on channel `w`. Each `g` copies tokens from its input to its output. Process `h` reads one token from the top input `t`, then one from the bottom input `u`, alternating, and writes each to the output.

!!! info "What you'll learn"
    - How blocking reads work: a process waiting on an empty channel may not look at any other channel
    - Why the output of a KPN does not depend on the order in which processes are fired (Kahn determinism)
    - How a blocked process wakes up when the token it is waiting for arrives
    - Why a "first available" merge is not a Kahn process, and how it makes the output depend on timing

---

## How to Use

**Be the scheduler:** press `f`, `gu`, `gd` or `h` (or click a process in the picture) to fire it once. A process that tries to read an empty channel turns red and blocks until a token arrives on that channel.

**Example schedule:** **Example schedule: next** steps through the schedule used in the lecture slides, `f f f f f gu h h gu gd gd gu h h h`. **Play example schedule** runs it automatically.

**Random schedule:** fires random unblocked processes until no process can make progress.

**Input stream:** type up to 14 whole numbers separated by spaces, then press Enter.

**How h chooses its next input:** switch between the Kahn process (strict alternation) and a non-Kahn merge that reads whichever input received a token first.

**Try 200 random schedules** runs the network to completion under 200 random firing orders and counts the distinct outputs.

Keyboard: Space plays the next step.

---

## Interactive Tutorial

<a href="../embeds/kpn-scheduler.html" target="_blank" rel="noopener" class="md-button">&#x2197; Open in new window</a>

<iframe src="../embeds/kpn-scheduler.html" title="Interactive Kahn process network scheduler" class="tutorial-frame" width="100%" height="1150" loading="lazy"></iframe>

---

## Key Observations

!!! example "Things to try"
    1. Step through the example schedule with the default input `4 1 7 2 6`. The second `h` blocks on `u`; firing `gu` does not wake it, because `h` is waiting on the bottom input, not the top. It wakes only when `gd` delivers `1`. The final output is `4, 1, 2, 7, 6`.
    2. Reset and fire `f` five times, then `h` repeatedly, then the `g` processes in any order you like. The output is still `4, 1, 2, 7, 6`.
    3. Press **Try 200 random schedules** in Alternate (Kahn) mode. Every schedule gives the same output.
    4. Switch to **First available** and try 200 random schedules again. Several different outputs appear, because `h` now tests which channel has data, which a Kahn process may not do.
    5. Enter an input with more evens than odds, such as `2 4 6 1`. The network stops with `h` blocked on `u` and tokens left on `t`: determinism says nothing about whether every token is consumed.

---

## Related Material

- [KPN and Synchronous Dataflow](../specification/dataflow.md)
- [Models of Computation](../specification/models-of-computation.md)
- [Overheat monitor, three MoCs](moc-overheat-monitor.md)
