The mentioned patterns in this chapter cut across all parts of the system. They don't fit neatly into the categories of error detection, error recovery, error mitigation and fault treatment. They don't focus solely on a particular class or method. They influence the design of the whole system and are also along the first applied patterns to a new design project make the system more fault tolerant. 

Each part of the system must support those patterns to actually profit of the implementation and system availability. 

The patterns are in strong relation to each other but Units of Mitigation are the building blocks of a fault tolerant system. 

# Correcting Audits
--------------------------------
## Intro
--------------

Design data to be checked and check data for errors. If errors are found, correct both the erroneous data and look for errors in related data 
## Problem
--------------------------------
Data errors occur when the data get corrupted, be it from low level hardware (memory chips) or application functions that store incorrect values into a data element. Data errors can also occur from random and transient events (alpha rays emitted from integrated circuit packaging).

Error from faulty data propagate throughout the system and the period of time between detection and recovery is long. The probability for another task to use the faulty data is high.
## Forces
--------------------------------------
**Faulty data causes errors** therefore it is important correct them quickly.
There are a wide range of different types of errors and therefore need different audits. The context of the data is important to decide what kind of checks need to be done and create an additional overhead and cost resources. There is also the risk of flagging data as incorrect even thought it is correct. The system must be designed in such a way that data can easily be checked.

The goal is to reduce downtime and increase system reliability by early error detection, correction and preventing those errors to propagate throughout the system. Also the additional logging gives better insight into the system. 
## Solution
----------------------------------
Checking or 'auditing' data for errors is accomplished by using other information that is known about the data and check if the context suits the data. For example 1974 is a valid year but not a valid age. Context helps to define the allowable data. 
Some ways the data can be checked: 

- **Check structural properties** (correctly linked lists, pointer within bounds, matching length)
- **Known correlation** (known conversion factors between data, same or related data is stored in multiple locations) 
- **Sanity checks** (Values within range)
- **Direct Comparison** (Duplicate copies of data)

When an data error is detected the correction mechanism varies and is based on the type of error.  Also when there is one data error there might be others and the detection mechanism should be as close as possible to the point of the first access. 

Examples for error correction mechanisms: 
- **Data Reset** 
- **Error correcting Code** 
- **Checksum**
- **Marking Data**  (Marking faulty data points)

When the error can not be corrected automatically an Error Handler, restoring data from a checkpoint or a rollback is needed. 

In order to detect the fault that is causing the incorrect data, the data error needs to be recorded and tracked. Repeated occurrences of events in the reports of data errors is useful to identify the underlaying fault. (logging) -> Fault Observer 

General Structure when data errors are detected:
1. Correction 
2. Logging
3. Resume execution 

# Redundancy
------------------------------------
## Intro
-----------------------------------
Error processing cannot begin until the error is detected. During Error recovery the system is unavailable. Minimizing this time period increases availability. 
## Problem
--------------------------------
When the system detects an error is must focus on processing the error and that usually stops the normal execution.  Error recovery returns the system to a failure-free state but require large amounts of time to recover. For example checkpoints or rollback require the time to copy large amounts of memory to reset the system to a certain state.  
## Forces
--------------------------------------
Redundancy is not free. There are the capital costs of extra resources such as memory or processing. There are also inherent risks and cost associated with maintaining the redundant elements . 
By introducing redundancy to a system, the system's availability can greatly be increased.  Informational redundancy enables the system access to alternate versions of the data that correcting audits can use to quickly make a correction. Redundancy can also be used to improve performance (Parallel Computation: Cluster).

## Solution
----------------------------------
The system can use different types of redundandy: 

Spatial: 
- Active - Active (total redundant units of mitigation, both active all the time and load sharing)
	-  Cost high, fastest recovery
- Active - Standby (standby element is in idle during normal operation) 
	- Cost high, slower recovery than active - active because the standby element related to how often they share state with each other.
- N+M redundancy (N active elements, M redundant elements)
	- Cost lower, but also slower recovery 

Temporal: 
- Recovery Blocks
Informational: 
- Multiple versions of data


## Prüfungsfragen
-------------------------------
	1. Frage **[Ja/Nein]**
	2.  Frage **[Ja/Nein]**
