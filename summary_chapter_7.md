###### Anton Paris & Fadil Smajilbasic

# Chapter 7: Error Mitigation Patterns

The Error Mitigation Patterns discussed in this chapter focus on strategies for mitigating errors in systems without altering the application or system state. The mitigation techniques mask errors and compensate for their effects. 
The Chapter can be broken down into different approaches: 

### Context of Error Mitigation:
Unlike the recovery methods discussed in the previous chapter, which involve changing the system state to continue operations, this chapter focuses on mitigating errors without such state changes. The techniques aim to mask errors and compensate for their effects.

### Types of Errors Addressed:
The chapter deals with two main types of errors: incorrect values or system states, and missed timing requirements or constraints. It presents methods to correct or change faulty values and states, or the underlying faults, allowing processing to continue from the point of error detection.

### Handling Overload and Performance Issues: 
The chapter also covers techniques for handling system overloads and performance issues caused by external factors, like an excess workload. These techniques are designed to help the system survive and return to normal operation without causing additional errors or failures.

### Multiple Faults and Data Errors:
Unlike most fault tolerant systems (as discussed in chapter 1) the mitigation techniques enable multiple faults within a system to exist and be contained, including data faults that can be marked and isolated (MARKED DATA). The chapter emphasizes that performance-related mitigation techniques do not hinder other error detection and processing activities.

### Timing Errors:
It discusses different techniques needed for various types of timing errors, like service request overloads, resource scarcity, or limited processor time.

### Key Premises and Patterns:
Several key premises and patterns are introduced, such as DEFERRABLE WORK, REASSESS OVERLOAD DECISION, EQUITABLE RESOURCE ALLOCATION, and SHED LOAD. These concepts focus on managing system resources efficiently and making dynamic decisions based on the current state of the system.

### Automatic Controls and Load Management:
It describes two categories of automatic actions in response to timing errors: expansive (expanding resources) and protective (protecting the system from overload). It also discusses strategies like SHEDDING LOAD, SLOWING IT DOWN, and FINISH WORK IN PROGRESS for effective workload management.

### Data Error Mitigation:
Chapter 7 touches upon mitigating data errors through marking unreliable data and using error-correcting codes when sufficient redundant information is available.

---

# Overload Toolboxes

## Intro

## Problem

## Forces

## Solution

## Example


---

# Deferrable Work

## Intro

## Problem

## Forces

## Solution


# Reassess Overload Decision

## Intro
Addressing a system overloaded with work requests, while utilizing error mitigation techniques.

## Problem
The system is overwhelmed with too many work requests, and its current methods to manage this, like SHED LOAD, are not effectively lessening the amount of work.

## Forces
- The necessity to efficiently manage and mitigate system overload.
- The difficulty in accurately identifying the root cause of errors or overload.
- The potential for system downtime or reduced effectiveness if it cannot process work efficiently.

## Solution
### Feedback Loop Implementation:
Introduce a feedback loop for the system to reassess its FAULT CORRELATION decisions. This loop is crucial for determining whether issues are related to timing or execution errors, enabling the exploration of alternative error processing strategies.
### Escalation Capability:
Design the system with the ability to escalate error analysis. This feature allows the system to test different mitigation techniques or to activate an escalation chain for error recovery. This escalation is essential for addressing situations where initial mitigation strategies are insufficient.

This approach is grounded in control theory, ensuring that the system does not continue down an ineffective path due to a lack of reexamination. The analysis should be conducted by SOMEONE IN CHARGE, who has an overview of the entire system.

---


# Equitable Resource Allocation

## Intro
 Equitable Resource Allocation addresses the challenge of managing and allocating resources in a system with a variety of requests, each having different types and priorities.
## Problem
The system faces a high volume of requests for resources, such as database or network connections. While it tries to prioritize new work over old (FRESH WORK BEFORE STALE), this approach can lead to issues in prioritizing requests correctly, especially when they differ in type or priority.
## Forces
- The need to balance different types of requests (e.g., database queries vs. order placements).
- The challenge of managing requests with varying priority levels (e.g., salesmen vs. ordinary users).
- The risk of resource overload and the inefficiency of processing pools if requests are not allocated properly.
## Solution
Implement a system that pools similar requests and allocates resources based on their availability and priority. This system should:
- Fairly process all requests, either in order or by periodically prioritizing more important requests.
- Avoid priority inversion, where lower priority tasks block higher priority ones by holding needed resources.
- Use QUEUE FOR RESOURCES to group and manage request pools efficiently.
- Include FINAL HANDLING for abnormal request terminations to release held resources effectively.
## Example:
In a scenario where many database queries and a few order placements are received, the system should not just serve the newest requests. Instead, it should group similar requests into pools and allocate resources based on each pool's priority and resource needs. This approach ensures that all types of work are accomplished, even in the presence of concentrated overloads from specific request categories or priorities. 

---

# Queue for Resources

## Intro

## Problem

## Solution

## Example

---


## Questions

1. The Equitable Resource Allocation pattern always prioritizes newer requests over older ones.
2. 

## Answers

<details>
  <summary>Answer 1</summary>

  **FALSE**: It advocates for a more balanced approach where resources are allocated based on both the availability and priority of different types of requests.

</details>

<details>
  <summary>Answer 2</summary>

  **FALSE**: 

</details>
