# Chapter 4: Architectural Patterns

We continue describing the patterns mentioned in chapter 4 of the book Fault Tolerance.

<div style="width:60%;margin:auto">
	<img src="./patterns_map.png" ></img>
</div>

## Someone in charge

### Intro

Anything can go wrong, even during error processing. When this happens the system might stop doing the error processing in addition to not doing the normal processing.

### Problem

Detection is hard; recovery is also hard. Within the world of fault tolerance, there are two kinds of detection:

1. Detecting the actual error
2. Detecting the part of the system that has failed silently

If that part of the system knows what should be happening, then the system is robust and it can fix the problem by itself or report it to the _FAULT OBSERVER_

If something does not work there must be part of the system that can restart processing actions to resolve the situation.

### Forces

##### Redundancy

If the system has multiple active elements, as can happen when they share the workload, develop some heuristic that will identify which is in charge during error processing.

##### Single component in charge of error processing

A problem with having a single component in charge of fault tolerance activities is that it provides a single point of failure. For any individual fault tolerance related action there must be a clearly identified or identifiable responsible entity. The responsibility can also be distributed across the system.

The module that performs the management can get complicated if it has to administer many different kinds of activities.

##### Dual Masters

The problem of ‘dual masters’ arises when more than one element claims to be *SOMEONE IN CHARGE*. A similar problem arises in *VOTING*. The system must have an automatic way of resolving this situation. Two simple techniques to resolve the issue are to use a lower address or an earlier processor start time to decide which should be considered to be in charge

### Solution

All fault tolerant related activities have some component of the system (‘someone’) that is clearly in charge and that has the ability to determine correct completion and the responsibility to take action if it does not complete correctly. If a failure occurs, this component will be sure that the new failure doesn’t stop the system.

The component in charge must be able to monitor the progression of actions taken to process the error and if they become stalled it must be able to initiate alternative actions, possibly _ESCALATION_ to 
more drastic measures.

Sometimes the FAULT OBSERVER (10) or the SYSTEM
MONITOR (15) perform dual rolls and serve as the elements
in charge in addition to their other responsibilities

**A Responsibility list**
| Action        | In Charge   |
| ------------- | ----------- |
| Checkpointing | Each task   |
| Rollback      | Component R |
| Roll-Forward  | Component R |
| Load Shedding | Component S |

The general techniques of *SYSTEM MONITOR*, including *HEARTBEATS* and *ACKNOWLEDGEMENTS*, can be used to ensure that the system has not silently failed

### Example

-   The caller of a method or function is in charge of that relationship.
-   A module in a system that is responsible fro the proper initialization and startup.
-   A management node in a cluster of systems

---

## Minimize Human Intervention

### Intro

### Problem

### Forces

### Solution

---

## Maximize Human Participation

### Intro

### Problem

### Forces

### Solution

---

## Maintenance Interface

### Intro

### Problem

### Forces

### Solution

---

## Fault Observer

### Intro

### Problem

### Forces

### Solution

# Questions
