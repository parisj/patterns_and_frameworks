# Glossar

## Fault
Is the defect that is present in the system that can cause an error. It is the actual deviation from the correctness. Often called a bug.  
In general, neither the software nor the observers are aware of the presence of a fault until an error occurs. Multiple faults create the same error and in the same failure. 
### Types: 
- Latent 
- active 
### Examples
- Incorrect Requirement Specification 
- Incorrect Designs
- Coding Errors 

### Single Faults
The assumption is that only one error will occur at a time. Error are independent of each other and recover from it has completed before another error occurs. 
-> how many redundant units are required to tolerate independent faults of thre kinds: 

| minimum number of components to tolerate fauilures | Type of Failure      |
| -------------------------------------------------- | -------------------- |
| n + 1                                              | Fail silent failures |
| 2n + 1                                             | Consistent failures  |
| 3n + 1                                             | Malicious failures   | 


## Error
Is caused by a Fault and is that part of the system state that is liable to lead to subsequent failure. It is the incorrect system behavior from which a failure may occur. Errors are important when talking about fault tolerant systems, because they can be detected before they become failures. 

### Types: 
- Timing
- Value

### Examples
- Timing or Race conditions 
- Infinite Loops
- Protocol Error (error in the messaging stream, unexpected message, out of sequence, inappropriate times, )
- Data inconsistency (elements in a network or data memory and disk different)
- Failure to Handle Overload conditions
- Wild Transfer of Wild Write (Data written to an incorrect location)

- Errors can be detected before they become failures
- Errors are the way that we can look into the system to discover if faults are present
- 
## Failure
Failure occurs when the system no longer complies with the specification 

### Types: 
- Fail-silent: failing unit either presents the correct result or no result at all
- crash Failure: Unit stops after the first fail-silent failure 
- Fail-stop: if the crash failure is visible to the rest of the system 
- Consistent: Failure appears the same each time it is observed and seen as the same kind of failure by all users or observers of a system 
- Inconsistent: Appear different to different observers (very hard to detect and to correct)


Questions: 

Errors are the way that we can look into the system to discover if faults are present [Ja]


## Examples of Fault -> Error -> Failure

A robotic arm used to drill a part in a manufacturing environment 
Fault of a misplaced decimal point in a data constant in the computation of the rotation of the robot's arm. (Steps required to rotate the robotic arm one degree). The error might be that it rotates in the wrong direction because of the faulty decimal point. The arm fails by lowering its drill at the wrong location 

## Coverage
Coverage is the conditional probability that the system will recover automatically within the required time interval that an error has occurred and is calculated from the probability associated with detection and recovery.  
## Reliability
Reliability is the probability that it will perform without failures during a specified time 

**Mean Time to Failure (MTTF)**: Average time from start of the operation until the time when the when the first failure occurs. 
**Mean Time to Repair (MTTR)**: Average Time required to restore a failing component to operation (replace faulty hardware with addition to the time to travel to the site)
**Mean Time between Failures (MTBF)**: Average Time from the start of operation until the component is restored (MTTF + MTTR)
### Examples
Airplane Navigation System: While the airplane is in air, the navigation system must be failure-free for between four and five hours: MTTF must be greater than five hours. Otherwise the crew could expect at least one failure on their flight. MTTR must be low because planes are required to be highly available with preferred no down time. 

### Measuring Reliability
Watch the system for a long time and calculate the probability of failure at the end of the time. Or predict the number of faults and then predict the probability of failures. 

## Availability 
Uptime: System is available
Downtime: System is unavailable
Availability is often expressed in terms of a number of nines: 

| Expression      |         | Minutes per year of downtime |
| --------------- | ------- | ---------------------------- |
| ---             | 100%    | 0                            |
| Three 9s        | 99.9%   | 525.6                        |
| Four 9s         | 99.99%  | 52.56                        |
| Four 9s and a 5 | 99.995  | 26.28                        |
| Five 9s         | 99.999  | 5.256                        |
| Six 9s          | 99.9999 | 0.5256                             |

$$\text{Availability} = \frac{\text{MTTF}}{\text{MTTF}+\text{MTTR}}$$
### Example
The 4ESS Switch from Alatel-Lucent had an explicit requirement of two hours downtime every 40 years. So it must be slightly better than five 9s. 
## Dependability 
Dependability is a measure of a system's trustworhiness to be relied upon to perfom the desired function 
**Safety**: non-occurence of catastrophic faules
**Security**: unauthorized access or unauthorized handling of information. Dependability includes bothe reliability and availability. The correctness of the result is important. 

### Reliability Engineering and Analysis

## Performance 

