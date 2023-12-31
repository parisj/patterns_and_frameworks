###### Anton Paris & Fadil Smajilbasic

# Chapter 5: Detection Patterns

---

# System Monitor

## Intro
The design of the system is for it to exhibit crash failures, when it silently stops working. The system needs to find out as quickly as possible so that recovery techniques can be applied. FAULT OBSERVER is used to coordinate information Flow. If a system fails silent the error propagation can be reduced but someone needs to get informed about the failure. For this task, there is a System Monitor pattern that lays the base for detection of system failures and communicates with FAULT OBSERVER. 
## Problem
The issues when a system stops working is that it preferrably is fail-silent to reduce the propagation of the error and therefore detection becomes challenging. It is also a challenge to find the error a part of the system malishious fails and detect the failure. 
## Forces
The systems architecture and the criticality of components play a role in determining the monitoring approach and can differ from system to system. 
The system needs a balance between not overusing network and and processing resources, by avoiding agressive error reporting, and the need to detect failures.
## Solution
A System Monitor is implemented to observe system behavior or specific parts to ensure continious operation
The System Monitor reports to the FAULT OBSERVER and initiate corrective action upon detecting a failure. 

A System Monitor can be implemented in different ways
  - Choosing the best suited monitoring method: HEARTBEAT, WATCHDOGS, ACKNOWLEDGMENT 
  - Setting realistic thresholds for error detection
The location of the System Monitor can also differ depending on the system's complexity and reliability requirements:
  - Implement as part of the FAULT OBSERVER
  - A Separate Element (on the same hardware and system) 
  - Using specialised Hardware (different Hardware component for monitoring)
In systems with extreme fault tolerance requirements the FAULT OBSERVER is frequently the entity that takes a global view of the situation and decides on the best processing steps to take. (Role: SOMEONE IN CHARGE) 
## Example

It is better for an ATM to stop taking customer requests than to wrongly dispence money while it is processing an error. (Accuracy) 
If Availability is more important than absolute correctness, options like REDUNDANCY, RESET or HUMAN INTERVERNTION might be used (Availability) 

---

# Heartbeat 

## Intro

Both silent and crash failures are possible. They must be detected as quickly as possible to minimize the Mean Time To Repair attribute of availability

## Problem

How does the _SYSTEM MONITOR_ know that a particular monitored task is still working?

## Forces

### Regular heartbeats

The health reports, or heartbeats, should occur at regular intervals. The timing enables the _SYSTEM MONITOR_ to detect that the monitored task has fallen behind or missed a report. _REALISTIC THRESHOLD_  provides guidance for setting appropriate intervals.

##### Pings

A ‘ping like’ message is a very simple way for the _SYSTEM MONITOR_ to ask if the monitored task is still processing. 
The message will stimulate an _ACKNOWLEDGEMENT_  response

##### System calls

Another way for the monitored task to report that it is still processing is for it to regularly execute a system call that was created for heartbeating purposes. Failure to execute the system call at a predetermined interval indicates a failure.

### Requesting heartbeats

Sometimes the task being monitored does not realize that it is being watched. This occurs when an existing application is being integrated into a highly available environment. 

The monitored task might have such an important place in the system that it assumes that it always will be available so it does not implement any kind of status reporting.

In these cases the _SYSTEM MONITOR_  should initiate a request for a status report or ‘heartbeat’

### Calculate in the heartbeats

You must weigh the time overhead associated with adding messages or other fault tolerance related actions with the benefit of the coverage that they provide.

During idle periods the overhead will be negligible. 

When the system is busy heartbeat messages can lead to extra messages that aren’t necessary because the stream of _ACKNOWLEDGEMENTS_ is giving sufficient status information. In this case you can use the correct processing of a sequence of requests as a sign of life.

##### Disable the heartbeats when unnecessary 

A _REALISTIC THRESHOLD_ should be put in place when the system is busy to reduce the the unnecessary overhead that is draining processing and messaging bandwidth from real work.

Building the intelligence to vary the thresholds (REALISTIC
THRESHOLD) based upon workload level adds complexity
and introduces faults.

## Solution

The _SYSTEM MONITOR_ should see a periodic heartbeat from the monitored task. If the monitored task does not supply a heartbeat response within the required time then recovery action should be taken.

<div style="display:block;height:200px;text-align:center">
	<img src="heartbeat2.png" style="position:relative;margin: 0px 50px">
	<img src="heartbeat.png" style="position:relative;margin: 0px 50px">
</div>
<br>
<br>

There are two variants to this solution: In the first one the monitored task sends a periodic, autonomous report to the _SYSTEM MONITOR_ to show that it is alive.

In the second the SYSTEM MONITOR requests the monitored task give it a report. 

Choosing a variant depends on:
- The complexity that will be built into the _SYSTEM MONITOR_ 
- if the monitored task can be modified with the automatic capability.

Heartbeats are useful when the monitor is a person (loading screen, progress bar, etc.).

#### Integration of heartbeats With the other units of mitigation

- _REALISTIC THRESHOLD_ offers guidance on the frequency of the heartbeats.
- _WATCHDOG_ describes a way of monitoring a task by watching its interactions with the rest of the system.
- If the heartbeats arrive, but don't look right, the _SYSTEM MONITOR_ must decide if it was a fault in the communications system, or if the monitored system is alive – but not well.
- _FAULT CORRELATION_ is needed to ensure that communication failures are detected.
- Detection of message delays is input to the selection of the correct _OVERLOAD TOOLBOX_  of mitigation techniques that should be employed.

---

# Acknowledgement

## Intro

Normal operations consist of a request from one task to another. It may or may not have a reply. The target of the request is the system to be monitored. 

This could be either a client-server system or a system that requires peer to peer communication.

In these systems there is a two way message flow between the tasks.

In some cases the system **only** needs to know if a crash failure has occurred when it tries to use the crashed (and monitored) task.

## Problem

When there is a dialog between two task, what’s the easiest way for one task to determine that the other task is alive and functioning?

## Forces

#### Use Heartbeats

You could add extra overhead by adding in separate mechanisms to report a failure, such as _HEARTBEAT_  messages but they add complexity and hence the potential for further faults.

#### Use Watchdog

You could add the hardware or software elements to act as a _WATCHDOG_ but that also adds complexity.

#### Piggybacking

An easy way is to add acknowledging information **to** a reply that is (or will be) sent.

This results in some additional complexity in both parties.

**Disadvantage**: if there are no requests, then there are no replies and hence no acknowledgements that can report a status.

##### Interim Acknowledgement while piggybacking

If the request will take a long time then the requestor might think that the request has died. 

In these cases an interim acknowledgement that merely **acknowledges receipt** of the request can help. A response to the reply will be sent later, when the request completes.

## Solution

1. Send an acknowledgement for all requests
2. All requests should require a reply to acknowledge receipt and to indicate that the system is alive and able to adhere to the protocol

#### What to do when no reply is received

If an acknowledgment reply is not received a failure should be reported to the _FAULT OBSERVER_ and error processing should be initiated.

_A REALISTIC THRESHOLD_ can be set which can lead to better performance and a reduced number of false alarms if an acknowledgment is not received.

#### Problem with this pattern

This pattern does not address the situation where the target task is responding to requests with acknowledgements, but is not sane and is not processing the requests. 

Some other mechanisms such as _CHECKSUMS_, _WATCHDOG_ or _COMPLETE PARAMETER CHECKING_ are needed.

## Example

<img src="./ack.png" style="display:block;margin:auto">

---


# Watchdog

<img src="watchdog.png" style="display:block;width:200px;margin:auto">

## Intro

You are implementing a _SYSTEM MONITOR_ . You are worried about adding complex software to the system because that can reduce the reliability.

## Problem

How can the system ensure a task is alive with a simple mechanism and you can’t or don’t want to add to messaging or processing overhead

## Forces

### No extra messages

In many circumstances you can’t add in any new messages because of the bandwidth limitation. 
If something is faulty it may not report its state correctly. 
Status reporting capability adds complexity, and hence the chance for additional faults.

### Add checks 

If you can’t add messages to the system, you can add checks on the validity of the tasks operations. 
One way discussed in other patterns is the redundancy based detection of errors by introducing _REDUNDANCY_ or _RECOVERY BLOCKS_  

These techniques are very expensive, in terms of both processing resources and complexity.

### Watch activities that happen routinely

One way of monitoring the system without increasing the complexity is to watch activities that happen routinely.
Example: looking if communications are happening as expected
- Are messages going both ways? 
- Are they frequent enough?

The monitor doesn’t become part of the message stream, it watches from afar.

### Use timers (timeouts)

Another technique that adds only a little to the complexity of the system is to set a hardware time before a critical operation is started.

During and at the end of the operation the timer is checked to determine if the timing was within acceptable limits.

### Add hardware

Sometimes you can add new hardware to the design. A simple hardware component that will watch the monitored task’s execution can be built. 

Possible uses:
- Monitor the control leads of a microcontroller
- Watch a word in memory that the task is known to use, or it could be a passive observer
watching an exchange of messages

Even if new hardware cannot be added the monitor can watch some kinds of things from another process on the same processor or even from another general purpose processor (as contrasted with dedicated watching hardware)

## Solution

Add in the capability for the monitor to observe the monitored task’s activities. 

A Watchdog can be either a hardware or a software component depending on the system requirements, but in either case it will watch visible effects of the monitored task. 

The monitored task will not be modified. 

The Watchdog should take some actions on the monitored task if it doesn't comply with the desired behavior.

<img src="watchdog_example.png" style="display:block;margin:auto">

One way of implementing this is via peepholes or hardware test points to enable the Watchdog to look inside the tasks.

This pattern is very similar to _SYSTEM MONITOR_. 
A key difference is that a _SYSTEM MONITOR_ **watches a number of tasks**
A _WATCHDOG_ is assigned to monitor **only one**. 

A _WATCHDOG_ will report to a _SYSTEM MONITOR_ and to a _FAULT OBSERVER_ when it detects a failure.
_SYSTEM MONITOR_  describes the kinds of things that a _WATCHDOG_ can do if it detects that the monitored task is not behaving properly.

---

# Riding Over Transients

## Intro
The System operates in an environment with many potential errors and faults, not all of which cause permanent failures. Transient faults and errors are temporaty and resolve on their own and need no error recovery.
## Problem
If every error or fault detected is recovered and handled, the system wasted resources processing transient errors that wont have a long term effect on the system. 
## Forces
The need to quickly detect and process errors must be balanced with the risk of reacting to transient errors that might resolve themselves. Differentiating between transient and non transient errors is crucial, as one needs no attention where as the other must be handled as quickly as possible.
Errors have signatures that can be indentified by FAULT CORRELATION. These signatures help categorize the errors and decide if an error is transient or requires immediate action.
## Solution
- Implement monitoring and fualt correlation to identify errors. Begin error processing immediately for non-transient errors. 
- For potential transient fault, monitor the frequency but delay action unless the occurrences exceeds expected levels.
- Set thresholds for tolerating transient errors based on the system's context and the risk of error propagation

## Example
- In disk operations, transient errors might be ignored under the assumption of disk reliability, riding over the transient.
- For web requests, users often re-request pages quickly, effectively riding over transient network errors.
---

# Leaky Bucket Counter

## Intro
Leaky Bucket Counter is an implementation of RIDING OVER TRANSIENTS, and is designed for systems that need to automatically recognize and correct errors, distinguishing between transient errors and repeating permanent faults. Each UNIT OF MITIGATION that is to be watched has a Leaky Bucket Counter. 


## Problem
The Challenge is determining wheter an error is transient (temporary) or a repeating permanent fault that requires correction. Errors are initially detected and classified. Some errors can be immediately identified as critical or non-transient based on their signatures and require immediate action. 
Other errors, particulary those that are not clearly critical or might be transient still need a way of handling them appropietly. 
## Forces
The system my encounter various errors, some of which are transient like signal crosstalk or static dicharges, and others that are permanent.
There is a need to balance the tolerance for transient errors against the necessity to act on permanent faults. So a kind of measuring needs to be introduced to decide if a error needs more attention. 

## Solution
- Implement a Leaky Bucket Counter for each unit of mitigation. The counter is incremented with each error and periodically decremented.
- If the counter exceeds a predetermined threshold it is a sign that they are permanent rather than transient errors.
- The rate of decrementing the counter (leak rate) is crucial and should be set to reflect the system's tolernace for transient errors. 
## Example
In the No. 4 Electronic Switching System (4ESS Switch) triggers error recovery when the number of occurences exceeds three, with the leak rate of 30 seconds

---

## Questions

1. With acknowledgments we can be sure that the system is alive and is  functioning correctly. True or False?
2. The Leaky Bucket Counter also counts the already known critical errors and waits for the bucket to "overflow" before handling them.

## Answers

<details>
  <summary>Answer 1</summary>

  **FALSE**: Acknowledgments only tell us that the system is alive and is able to respond. It does not tell us that the system is functioning correctly.

</details>

<details>
  <summary>Answer 2</summary>

  **FALSE**: Known critical errors are handled immediately 

</details>
