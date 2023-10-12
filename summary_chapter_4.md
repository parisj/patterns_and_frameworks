# Chapter 4: Architectural Patterns

The mentioned patterns in this chapter cut across all parts of the system. They don't fit neatly into the categories of error detection, error recovery, error mitigation and fault treatment. They don't focus solely on a particular class or method. They influence the design of the whole system and are also along the first applied patterns to a new design project make the system more fault tolerant.

Each part of the system must support those patterns to actually profit of the implementation and system availability.

The patterns are in strong relation to each other but Units of Mitigation are the building blocks of a fault tolerant system.

![Architectural pattern language map](./patterns_map.png)

# Units of Mitigation

## Intro

The Units of Mitigation are the building blocks of a fault tolerant system. They define the elements that may be repaired and restarted as needed to process errors. The units of mitigation tell us how redundancy will be implemented in the system.

When thinking about Units of mitigation, you are at the early design stages where the system design is still malleable. You want to reduce, or mitigate, this risk of a complete shutdown.

Portions of the system that perform different functions, such as interfacing to users or handling a database, are good units of mitigation. When there are separate processors in a distributed system, these make good units of mitigation. When there are groupings of similar functionalities, the entities that are grouped are good units of mitigation. For example threads in a threadpool make good units of mitigation

##### Key Points:

Units of mitigation:

-   Should not share more than one processor unless
    there is shared memory, or more than one region of non-distributed memory. Error detection and processing techniques work best with clear boundaries of memory.
-   Should be able to conduct self checks
    to detect when they are not operating correctly.
-   Should fail in a deterministic way.
-   If they are so small that any errors that occur cannot be recovered or mitigated, the unit is too small.
-   Should be able to be restarted without affecting the rest of the system.
-   Must be barriers to errors.
-   Must detect internal errors quickly.
-   Only a single indication that an error has occurred is an acceptable indication to other parts of the system.
-   It is desirable for units of mitigation to fail silently and to isolate failures from other parts of the system.

## Problem

How can you design the system to be available even when errors occur?

In the simplest designs, there is one module that performs all of the work.
You know that there will be faults in the system. 
Having only one module means that the entire system must stop to recover an error.

## Forces

#### Multi-module design

In a multi-module design, the system is divided into multiple modules. Each module performs a specific task. The modules communicate with each other to perform the overall system function.

Using this design we unlock more possibilities to recover from errors. We can now recover from errors in one module while the others continue operation.

Some of the techniques possible include FAILOVER , retaining state via CHECKPOINTS , redirecting operations through ROLLBACK or ROLL-FORWARD (Will be described later).

###### Overhead

The amount of overhead increases as the number of the components increase.
An example of static overhead is the extra memory that is being used.
An example of dynamic overhead is the execution time of the system, which can be increased by the extra communication between the modules.

#### Interfaces

Interfaces between the units of mitigation must be well defined and clear. It is along these interfaces that the mitigation actions take place to recover from errors or reduce the impact of errors. When there are more units in the system, there are more interfaces.

The units of mitigation should contain atomic actions that do not rely heavily on communication with other units of mitigation to accomplish their task.

The dividing line between different parts of the system must be clearly recognizable and the boundary must be respected. This will allow for errors to be contained and not spread to other parts of the system.

#### Architecture

No two design problems are identical. There is no right answer to the question about what the basic units should be.

The architectural style in use, such as a three-tiered architecture (presentation, application and data tier), can provide units of mitigation that are similar from situation to situation. But even in that case you need to determine if there are other units present in the architecture.

## Redundancy

Adding additional redundant units helps with both performance enhancements
by supporting load sharing, and with error processing. The system can redirect the workload to a different unit if one needs to be removed from service temporarily to support error processing.

## Solution

Divide the system into parts that will contain both any errors and the error recovery. Choose the divisions that make sense for your system. Design the rest of the system around these parts that represent the basic units of error mitigation. The actual resolution of this problem will vary with the situation and will result in analysis of the various tradeoffs/forces: Overhead and complexity, natural divisions in the architecture and architectural style basic concepts.

## Example

Imagine a system with a three-tiered architecture^: user interface, a database and a processing unit.

|      | Single mitigation unit                                | Tier-based mitigation unit                                                     | Redundant tiers or queues                                                                                  |
| ---- | ----------------------------------------------------- | ------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------- |
| Pros | Common approach,<br>Used by many systems              | Easy to define the units,<br> clean interfaces between tiers                   | The system can redistribute the workload if a single tier error occurs,<br>Queue buffers incoming requests |
| Cons | when something fails the entire system is unavailable | any tier level error processing actually takes the whole system out of service | Adds complexity and further consideration into the whole system architecture                               |

# Correcting Audits

## Intro

Design data to be checked and check data for errors. If errors are found, correct both the erroneous data and look for errors in related data

## Problem

Data errors occur when the data get corrupted, be it from low level hardware (memory chips) or application functions that store incorrect values into a data element. Data errors can also occur from random and transient events (alpha rays emitted from integrated circuit packaging).

Error from faulty data propagate throughout the system and the period of time between detection and recovery is long. The probability for another task to use the faulty data is high.

## Forces

**Faulty data causes errors** therefore it is important correct them quickly.
There are a wide range of different types of errors and therefore need different audits. The context of the data is important to decide what kind of checks need to be done and create an additional overhead and cost resources. There is also the risk of flagging data as incorrect even thought it is correct. The system must be designed in such a way that data can easily be checked.

The goal is to reduce downtime and increase system reliability by early error detection, correction and preventing those errors to propagate throughout the system. Also the additional logging gives better insight into the system.

## Solution

Checking or 'auditing' data for errors is accomplished by using other information that is known about the data and check if the context suits the data. For example 1974 is a valid year but not a valid age. Context helps to define the allowable data.
Some ways the data can be checked:

-   **Check structural properties** (correctly linked lists, pointer within bounds, matching length)
-   **Known correlation** (known conversion factors between data, same or related data is stored in multiple locations)
-   **Sanity checks** (Values within range)
-   **Direct Comparison** (Duplicate copies of data)

When an data error is detected the correction mechanism varies and is based on the type of error. Also when there is one data error there might be others and the detection mechanism should be as close as possible to the point of the first access.

Examples for error correction mechanisms:

-   **Data Reset**
-   **Error correcting Code**
-   **Checksum**
-   **Marking Data** (Marking faulty data points)

When the error can not be corrected automatically an Error Handler, restoring data from a checkpoint or a rollback is needed.

In order to detect the fault that is causing the incorrect data, the data error needs to be recorded and tracked. Repeated occurrences of events in the reports of data errors is useful to identify the underlaying fault. (logging) -> Fault Observer

General Structure when data errors are detected:

1. Correction
2. Logging
3. Resume execution

# Redundancy

## Intro

Error processing cannot begin until the error is detected. During Error recovery the system is unavailable. Minimizing this time period increases availability.

## Problem

When the system detects an error is must focus on processing the error and that usually stops the normal execution. Error recovery returns the system to a failure-free state but require large amounts of time to recover. For example checkpoints or rollback require the time to copy large amounts of memory to reset the system to a certain state.

## Forces

Redundancy is not free. There are the capital costs of extra resources such as memory or processing. There are also inherent risks and cost associated with maintaining the redundant elements .
By introducing redundancy to a system, the system's availability can greatly be increased. Informational redundancy enables the system access to alternate versions of the data that correcting audits can use to quickly make a correction. Redundancy can also be used to improve performance (Parallel Computation: Cluster).

## Solution

The system can use different types of redundandy:

Spatial:

-   Active - Active (total redundant units of mitigation, both active all the time and load sharing)
    -   Cost high, fastest recovery
-   Active - Standby (standby element is in idle during normal operation)
    -   Cost high, slower recovery than active - active because the standby element related to how often they share state with each other.
-   N+M redundancy (N active elements, M redundant elements)
    -   Cost lower, but also slower recovery

Temporal:

-   Recovery Blocks

Informational:

-   Multiple versions of data

# Recovery Blocks

## Intro

Recovery blocks are a way to divide the system into parts that can be recovered independently. The system can recover from errors in one block while the other blocks continue to operate.

The recovery blocks are Units of Mitigation, they contain errors and are the basic unis of redundancy in which error processing is possible.

A program with recovery blocks consists of a primary block and secondary blocks. If the result of the primary block fails its acceptance test then secondary blocks execute in sequence until the acceptance test passes. If no acceptable result is found then an error handler is invoked (ESCALATION).

## Problem

How can we make sure that processing results in an error-free value, when executing the same code repeatedly will produce the same error?

## Forces

#### Alternate implementations

Alternate solutions need different implementations.

When there are multiple redundant implementations the system can execute them simultaneously and pick the best answer using a combination of redundancy and voting.

Another way of using multiple implementations is to execute them sequentially. Overhead is limited because alternative implementations only execute when the system detects errors.

#### Simplification

A common approach with recovery blocks is to make each successive block more simple than its predecessor. Each has a higher probability of satisfying the acceptance test because it is more simple. Some information is lost at each successive block because it is not performing all of the actions that were initially to be done by the primary block.

#### Checkpoints

The system must be able to return to a previous state if the recovery block is unable to produce an acceptable result. Checkpoint assure that the system can run secondary blocks with the same state as the primary block.

#### Avoid many secondary blocks

Project needs and reliability predictions determine the number of secondary blocks. The time spent processing the same information in successive secondary blocks is time that the system is unavailable for other work. Balance the need to have the result and the ability to try different methods, with the overall system availability specification.

#### System complexity

Added system complexity due to the acceptance test and the lack of alternative algorithms are two drawbacks of recovery blocks. Shared, global data also reduces the effectiveness of recovery blocks.

Failure of a recovery block should not be used as an indication of a permanent fault in the system. The block may be reusable with different input or initial state

## Solution

Provide a diversity of redundancy implementations, either different designs or different coding. Execute them within a framework that checks for acceptable results from the execution of one and try the next secondary block, if the results were unacceptable.

## Example

Consider a case where a section of the program to sort an array is considered a _Unit of Mitigation_, different sorting algorithms are the **recovery blocks**.

The try/throw/catch mechanism of languages such as C++ and Java give one way to implement **recovery blocks**

# Escalation

## Intro

The system has tried many options to process or mitigate an error or failure in a component. Error processing has stalled without completing. Error processing must succeed to enable the system to resume normal operation

## Problem

What does the system do when its attempt to process an error in a component is not achieving the correct effect?

In most cases, though, simply repeating the same techniques is not the right thing to do. The system should limit retries to prevent being stuck in an endless loop.

## Forces

#### Endless loops

The error just gets stuck sometimes.

Sometimes the system can repeat the attempts to recover the component endlessly. This is the correct option if the attempts do not cause damage and if they might have a chance to work, particularly if the problem is actually a transient.

#### Drastic action

More drastic alternatives include making the error processing less local.

More and more processes, farther and farther from the faulty one, become involved in the recovery.

For example, a first attempt might be to correct an input and retry an action. If that doesn’t achieve success then the task that processes the input is restored to a pre-error state through a _checkpoint_ and _rollback_. If that does not succeed then the task is restarted. And if that doesn’t succeed then the task is assigned to a redundant processor.

#### Someone in charge

Escalation should report to _Someone in Charge_ (Component) for it to trigger some new action. Depending on the nature of the errors and failures, it might request that there be human intervention. You should ensure that people can override system behavior when absolutely necessary

For a human operator there should be a predefined list of recovery actions that can be taken. The list should start with those things that are fast, have a high probability of recovering and a minimal impact on other aspects of system operation; followed by other actions that are increasingly disruptive and slower.

## Solution

When recovery or mitigation is failing, escalate the action to the next more drastic action

At the end of the chain of Escalations, sometimes the best thing is to resume partial operation. Isolate the functionality that has the errors and allow the rest of the system to proceed as best as it can.

Humans will almost always have to step in and correct the situation, or at least tell the system to retry the error correction.

#### Identifying the escalation steps

The escalation steps are not generic. They depend on the nature of the system and the nature of the errors and failures that are occurring. The escalation steps are a part of the system design and should be identified as part of the design process.

# Prüfungsfragen

1.  A Tier-based mitigation unit is enugh to guarantee the availability of the system? **[Nein]**
2.  In most cases human intervention is neccessary at the highes level of an Escalation chain **[Ja]**
