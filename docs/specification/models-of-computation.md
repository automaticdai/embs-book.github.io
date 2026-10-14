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

## Running example: an overheat monitor

The sections below use one small system throughout. A board heats up. A sensor samples its temperature every 100 ms, an averager combines the last four samples, a comparator tests the average against 80 °C, and an LED turns on above that threshold. The same behaviour is described three times, once per model.

!!! tip "Interactive tutorial"
    Run all three versions side by side in the [overheat monitor tutorial](../tutorials/moc-overheat-monitor.md): step the CT solver, process the DE event queue by hand and act as the dataflow scheduler yourself.

## Continuous time

A CT model describes relations between continuous signals, often using ordinary differential equations. The board in the overheat monitor can be modelled as a lumped thermal mass:

$$C\frac{dT}{dt}=P-\frac{T-T_{\mathrm{amb}}}{R},$$

where $T$ is the board temperature, $C=12$ J/K its heat capacity, $R=10$ K/W the thermal resistance to ambient, $P=7$ W the dissipated power and $T_{\mathrm{amb}}=25$ °C. This equation is an illustrative model, rather than a hardware implementation. Starting from $T(0)=25$ °C, it has the exact solution

$$T(t)=T_\infty+\bigl(T(0)-T_\infty\bigr)e^{-t/\tau},\qquad \tau=RC=120\text{ s},\quad T_\infty=T_{\mathrm{amb}}+PR=95\text{ °C}.$$

Solving $T(t^*)=80$ gives $t^*=\tau\ln(70/15)\approx184.9$ s: the true moment the board crosses the alarm threshold.

A numerical solver approximates the trajectory at selected time points. Forward Euler with step $h$ computes

$$T_{n+1}=T_n+\frac{h}{C}\left(P-\frac{T_n-T_{\mathrm{amb}}}{R}\right).$$

With $h=60$ s:

| $t$ (s) | 0 | 60 | 120 | 180 |
|---|---:|---:|---:|---:|
| Exact $T$ (°C) | 25.00 | 52.54 | 69.25 | 79.38 |
| Euler $T_n$ (°C) | 25.00 | 60.00 | 77.50 | 86.25 |

The solver first reports the alarm at 180 s, about 5 s before the true crossing, and knows nothing about the signal between its steps. The error relative to $T_\infty$ is multiplied by $(1-h/\tau)$ on each step, so Euler zig-zags around the steady state when $h>\tau$ and diverges when $h>2\tau$. Step size and error tolerance therefore affect accuracy, stability and cost. The physical system is the same in each case; only its numerical approximation changes.

A detailed physical model can be connected to a discrete controller, but the boundary must specify sampling and actuation behaviour. In the overheat monitor, that boundary is the sensor sampling every 100 ms.

## Discrete event

DE components exchange events with a value and a tag. In the Ptolemy model discussed in the lecture, the tag includes a **timestamp** and a **microstep**. Microsteps distinguish logically ordered events at the same physical time.

The simulation processes events in tag order. For a zero-delay chain `sensor → controller → actuator`, an output may have the same timestamp as its input; dependency ordering and microsteps make the reactions well defined. Model time is distinct from the wall-clock time taken to run the simulation.

Events with equal timestamps need precise semantics. Arbitrary iteration order should not silently determine the result of the model.

### Overheat monitor as a DE model

In the DE version, a Sampler schedules a **pure event** (a wake-up with no data) for itself every 100 ms. When it fires, it sends the sample to both the Averager and a Display. The Averager sends its result to a Comparator and the Display; the Comparator drives the Alarm LED. Each actor has a **depth** from a topological sort of the graph: Sampler 0, Averager 1, Comparator and Display 2, Alarm 3.

The director keeps one global event queue sorted by timestamp, then microstep, then depth. At 0 ms it processes:

| Step | Actor (depth) | Inputs at 0 ms | Outputs, all stamped 0 ms |
|---|---|---|---|
| 1 | Sampler (0) | Pure event | Sample 70 to Averager and Display; wake-up booked for 100 ms |
| 2 | Averager (1) | 70 | Average to Comparator and Display |
| 3 | Comparator (2) | Average | `off` to Alarm |
| 4 | Display (2) | Sample and average | Shows both values together |
| 5 | Alarm (3) | `off` | LED off |

The Display receives a sample and an average at the same timestamp. Depth ordering makes it fire once, after the Averager, with both inputs; an arbitrary order could make it fire twice and show a stale average. No model time passes during these five firings. The next event in the queue is the wake-up at 100 ms, so model time **jumps** directly from 0 to 100 ms without simulating anything in between.

With samples 70, 75, 82, 88, 90, 72, 60, 55 and a four-sample sliding window, the average first exceeds 80 °C at 400 ms (83.75 °C), so the LED turns on then. It turns off again at 600 ms (77.5 °C).

## Untimed dataflow

Actors communicate through FIFO channels. Reading consumes a token. An actor's ability to run depends on the required inputs being available; the graph exposes dependencies and potential parallelism.

An untimed model can establish the sequence of computed results without establishing how quickly those results appear on a real device. Execution costs, mapping and scheduling must be added to analyse implementation timing.

In the dataflow version of the overheat monitor there is no clock. The Sensor has no inputs and may fire at any time; Average fires once four sample tokens are waiting, Compare when one average is waiting, and the LED when one Boolean is waiting. Any order that respects token availability produces the same LED sequence. The [SDF worked example](dataflow.md#worked-example-the-overheat-monitor-as-sdf) derives its schedule.

Continue to [KPN and synchronous dataflow](dataflow.md) for determinism, balance equations and buffer sizing.

## Combining models

A control application may use CT for the physical process, a state machine for operating modes and SDF for filtering. Hierarchy lets a composite component contain its own execution rules. The interface between domains must define how events, samples and time are translated; simply changing a director does not make every actor valid in the new domain.

See [Ptolemy II](ptolemy.md) for how actors, directors and receivers express these choices.

??? question "Check your understanding"
    A dataflow simulation produces the right sample sequence. Has it proved a 1 ms implementation deadline?

    No. The untimed model establishes functional behaviour. A timing argument also needs execution and communication bounds, a platform mapping and a schedule.
