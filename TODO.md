# Site improvement checklist

Remaining gaps identified by comparing the site with the lectures in `__ref/`.

## High priority

- [ ] **Resource sharing:** add worked PIP blocking calculations and execution timelines comparing PIP, OCPP and ICPP.
- [ ] **Multicore analysis:** add numerical remote-blocking and response-time examples, with explicit protocol assumptions and a clear distinction between MSRP and MrsP.
- [ ] **Priority assignment:** add a complete Audsley walkthrough, including the lecture's example where deadline monotonic assignment fails.

## Medium priority

- [ ] **Ptolemy implementation:** add a custom actor example with ports, parameters and lifecycle methods.
- [ ] **FreeRTOS implementation:** add a small application demonstrating tasks, queues and interrupt signalling.
- [ ] **Dataflow analysis:** add a worked inconsistent graph, matrix reduction and a cyclic example with initial tokens.
- [ ] **Hungarian mapping:** show the intermediate matrices and assignment steps for the existing worked example.

## Lower priority

- [ ] **Fixed-priority scheduling:** cover harmonic task families and the lecture's improved utilisation test.
- [ ] **Partitioning:** introduce the Fiduccia–Mattheyses algorithm and clarify its assumptions and complexity.
- [ ] **Multicore scheduling:** cover Pfair, EDZL and TkC/DkC policies, including their assumptions and trade-offs.

## Presentation and verification

- [ ] Add scheduling timelines to illustrate preemption, blocking and completion times.
- [ ] Add state diagrams for task states and reactive behaviour.
- [ ] Add dataflow graphs alongside the balance equations and buffer tables.
- [ ] Visually check mobile navigation, tables and mathematical layout in a browser.
- [ ] Verify new numerical examples and run `mkdocs build --strict` after completing content updates.
