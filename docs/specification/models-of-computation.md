# Models of Computation

*Lecture 6 · Computation Models*

A **model of computation (MoC)** specifies the rules for component execution and communication. A graph shows connections; the MoC gives those connections a meaning. The same graph can describe timed events, continuously varying signals or streams of data.

## Separate representation from meaning

A finite state machine can be drawn as a graph, written as a transition table or implemented in code. These are different representations of the same state-transition semantics. Conversely, two identical actor diagrams can behave differently if their execution rules differ.

For each model, ask: when may a component execute, what does a connection carry, how is time represented, and how are simultaneous activities ordered?

| Model | Communication | Time | Typical application |
|---|---|---|---|
| Continuous time (CT) | Continuous signals | A continuous time variable | Physical plant, analogue circuits, heat flow |
| Discrete event (DE) | Values tagged with event times | Ordered event tags | Digital systems, networks, queues |
| Kahn process network (KPN) | Token streams through FIFOs | Ordering of tokens | Concurrent stream processing |
| Synchronous dataflow (SDF) | Fixed token rates per firing | Usually untimed | Signal and media processing |

## Continuous time

A CT model describes relations between continuous signals, often using ordinary differential equations. A simple thermal model could be

$$\frac{d\theta}{dt}=-k(\theta-\theta_{\mathrm{ambient}})+u(t),$$

where $\theta$ is temperature, $k$ describes cooling and $u(t)$ represents heating in temperature-per-time units. This equation is an illustrative model, rather than a hardware implementation.

A numerical solver approximates the trajectory at selected time points. Its step size and error tolerance affect simulation accuracy and cost. A detailed physical model can be connected to a discrete controller, but the boundary must specify sampling and actuation behaviour.

## Discrete event

DE components exchange events with a value and a tag. In the Ptolemy model discussed in the lecture, the tag includes a **timestamp** and a **microstep**. Microsteps distinguish logically ordered events at the same physical time.

The simulation processes events in tag order. For a zero-delay chain `sensor → controller → actuator`, an output may have the same timestamp as its input; dependency ordering and microsteps make the reactions well defined. Model time is distinct from the wall-clock time taken to run the simulation.

Events with equal timestamps need precise semantics. Arbitrary iteration order should not silently determine the result of the model.

## Untimed dataflow

Actors communicate through FIFO channels. Reading consumes a token. An actor's ability to run depends on the required inputs being available; the graph exposes dependencies and potential parallelism.

An untimed model can establish the sequence of computed results without establishing how quickly those results appear on a real device. Execution costs, mapping and scheduling must be added to analyse implementation timing.

Continue to [KPN and synchronous dataflow](dataflow.md) for determinism, balance equations and buffer sizing.

## Combining models

A control application may use CT for the physical process, a state machine for operating modes and SDF for filtering. Hierarchy lets a composite component contain its own execution rules. The interface between domains must define how events, samples and time are translated; simply changing a director does not make every actor valid in the new domain.

See [Ptolemy II](ptolemy.md) for how actors, directors and receivers express these choices.

??? question "Check your understanding"
    A dataflow simulation produces the right sample sequence. Has it proved a 1 ms implementation deadline?

    No. The untimed model establishes functional behaviour. A timing argument also needs execution and communication bounds, a platform mapping and a schedule.
