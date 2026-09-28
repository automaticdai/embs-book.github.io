# Modelling with Ptolemy II

*Lecture 5 · Embedded Systems Specification II*

Ptolemy II illustrates how a modelling framework separates component structure from execution semantics. **Vergil** is its graphical model editor; **MoML** is the XML representation used to store models.

## Actors, ports and directors

| Construct | Responsibility |
|---|---|
| Actor | Computation, state, parameters and input/output ports |
| Port | An actor's communication interface |
| Relation | Connectivity between ports |
| Token | A communicated value |
| Receiver | Domain-specific handling of received tokens |
| Director | Execution and communication rules for a composite model |
| Manager | Coordination of a model execution and its lifecycle |

An **atomic actor** implements a component directly. A **composite actor** contains other actors. A director and the associated receivers implement a domain's [model of computation](models-of-computation.md).

An actor diagram alone cannot answer whether reads block, tokens have timestamps or a firing consumes a fixed number of inputs. These are semantic questions that depend on the domain and the actor's contract.

## Hierarchy and local semantics

A composite actor with a local director is **opaque**: its internal execution is governed through that director. A composite without a local director is **transparent**, so the surrounding director governs its contained actors. A local director permits a hierarchy of different domains. This follows the [Ptolemy director documentation](https://ptolemy.berkeley.edu/ptolemyII/ptII11.0/ptII11.0.1/doc/codeDoc/ptolemy/actor/Director.html).

## Actor execution lifecycle

| Method | Role |
|---|---|
| `initialize()` | Establish the initial state before iterations |
| `prefire()` | Report whether the actor is ready to participate |
| `fire()` | Compute outputs according to the current inputs and state |
| `postfire()` | Commit state changes and report whether execution should continue |
| `wrapup()` | Perform end-of-execution actions |

The director determines how these methods are invoked. Some domains can evaluate an actor more than once while resolving an iteration. Keeping persistent state updates in the appropriate lifecycle method avoids advancing state merely because the solver requested another evaluation.

## Build a small model

1. Choose a domain to match the question you want to answer. For a fixed-rate sample pipeline, use SDF.
2. Connect a source, a gain actor and a sequence display. Set the source and gain parameters.
3. Check port types and token production/consumption rates.
4. Configure a finite number of iterations and execute the model.
5. Predict the output sequence by hand, then compare it with the displayed values.

The lecture's larger example generates a sinusoid, multiplies it by a carrier, adds noise and estimates its spectrum. Its actor graph is useful because it makes the signal-processing stages and their connections explicit.

For a custom actor, specify both its computation and its domain assumptions. An actor that needs two tokens per firing must declare or enforce that requirement consistently; consuming arbitrary input amounts is incompatible with a fixed SDF rate contract.

??? question "Check your understanding"
    Does making a model hierarchical automatically give each subsystem its own timing semantics?

    No. A transparent composite inherits the surrounding execution semantics. A local director establishes an opaque execution boundary; any interaction across that boundary must still have a defined meaning.

Continue with [models of computation](models-of-computation.md), then [dataflow analysis](dataflow.md).
