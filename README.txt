Project Description:

You are asked to (1) implement Lamport's distributed mutex exclusion algorithm exactly as he described it in his CACM 1978 time-clocks paper in (different from the programs given in class), (2) describe at least 2 precise ways that the algorithm does not satisfy the specified safety and liveness requirements, and (3) design and implement methods for testing correctness and comparing performance of implementations of 3 distributed mutual exclusion algorithms written in DistAlgo.

For (1), you may reuse most of some example program in DistAlgo (lamutex/orig.da, under da/examples/), but make sure you follow the overall organization of the 5 rules exactly as described in the paper. Be careful. Don't be creative.

For (2), you may find two ways of violating safety, or liveness, or one of each. You need to describe each way precisely and why it violates safety or liveness. You may write an execution trace, draw a message sequence diagram, etc. Be concise. Be creative too if you like.

For (3), you are asked to test correctness and compare performance of implementations of 3 distributed mutual exclusion algorithms written in DistAlgo (your program from (1), lamutex/orig.da, and lamutex/spec.da, under da/examples/). The goal is to better understand these algorithms.

Your main task will be to design and implement methods for testing correctness and performance. Your implementation is expected to be in DistAlgo/Python.

For correctness, your program needs to be able to do a large number of runs on different parameter values, record information about critical sessions, and check that each run is correct.

For performance, your program needs to be able to do several different runs varying a particular parameter, with multiple repetitions of each run, and measure the running times, including elapsed time and total CPU time, and report statistics about them.

You may implement everything yourself or use implementations by others so long as you understand them well. (Reminder: anything that is not your own creation must be given exact sources and credits; follow the requirements on the course syllabus.)


Your program:

Your program must run under Python 3.6.5 and DistAlgo 1.1.0b12 (can just do "pip install --pre pyDistAlgo" if you have pip installed, or download a file from http://distalgo.cs.stonybrook.edu/download, and follow README).

Your main program must be named
"main.da", and must run with a command like the following, where 
"p" is for number of processes, 
"r" is for total number of requests, 
"n" is for number of runs for correctness testing, and 
"d" and "a" are for number of parameter values and number of repetitions, respectively, for performance testing:

python.exe -m da main.da p r n d a

and do the following two sets of tasks:

(1) for each of the different implementations, test correctness of "n" runs, with each run using randomly generated numbers of processes and requests, up to "p" processes and "r" requests.

(2) for each of the different implementations, compare performance of "d" different runs on "p" processes and up to "r" requests evenly spaced each process, and compare performance "d" different runs on up to "p" processes evenly spaced and "r" requests each process, with "a" repetitions for each run. For example, p = 10, r = 20, d = 5, a = 3 means: compare 5 runs on 10 processes and 4, 8, 12, 16, and 20 requests, respectively, each process, and compare 5 runs on 2, 4, 6, 8, and 10 processes, respectively, and 20 requests each process, where each run should be repeated 3 times.

You have the freedom to decide what information to print about the tests and in what table format. They need to clearly show correctness information and performance comparison. In particular, they must include at least safety violations, if any, and running times averaged over repeated runs, in two tables---one for varying request numbers, and one for varying process numbers.




Solution:

Brief Overview

Following is the short description of what the program main.da contains and what will it do:
Main.da: This is the main distal file which contains the implementation of all three implementation of Lamport's mutex algorithm.
Following are the main classes which have been implemented in main.da. Note all these classes inherit the process class.

1. class MainP : This class contains my implementation of Lamport's mutual exclusion algorithm.
2. class SpecP : This class has been taken from the already existing spec.da file in <DA_ROOT>/da/examples/lamutex directory.
3. class OrigP : This class again has been taken from the already existing orig.da file in <DA_ROOT>/da/examples/lamutex directory.
4. class StatMonitor : This is a special class which is implemented to monitor the performance of these algorithms. It acts as an alien process in the system and collects messages from all other process. Later these messages are used to check for the correctness of algorithm as well as the runtime analysis.

*****************************************************************************
NOTE: Due to IOS issues, I usually see some Network error, whenever I try to I
increase my number of processes to greater than say 10. This behaviour is 
consistent across all 3 algorithms and it occurs randomly.
*****************************************************************************

Ans 1)
As per Lamport’s Mutex Algorithm there is no restriction on the execution sequence of each rule Whereas, there is a problem with the interpretation of the word ‘any’ in the RULE 3 and 4 of the his paper. I have implemented an algorithm just like he mentions in his paper where send(), enter_critical_section() and release() calls can interleave for a process. This algorithm when run results into deadlock, and safety violations. I obtained a deadlock trace with this implementation and have placed the execution trace(deadlock.png) in the zipped file. This incorrect interpretation of the algorithm can be reproduced in the code as well by commenting out some part of the code in the receive handler for the release event. I have placed the wrong implementation as well in the zipped file. There is also note in the code which stated which part to enable for seeing deadlocks. The correct interpretation of the word ‘any’ however means the oldest or the first request that was generated by that process.
This problem may not appear if the send and release are done in alternate fashion which guarantees that at particular time a process will always have only single request of its own in the queue, thus removing the ambiguity associated with the word ’any’.


Ans 2) 
For this problem, please refer to Answer2.pdf in the zipped file.


Ans 3) 
Correctness Validation:
This required the implementation of the StatMonitor class. Most of the validation part for safety and liveliness principles is done in this class. Following are the two methods in the class which checks for the correctness of a particular run.
•	validate_safety() : Checks for the condition where two processes entered the critical section at the same time.
•	wait_wrapper() and deadlock_handler(): Both these methods together provide a way to check if the system has gone into a deadlock by setting ‘off’ an alarm which is then caught by the deadlock_handler to kill the processes.

Output File: Output is generated in the “Correctness.csv” file in the current working directory of the user.

Performance Validation:
Most of the intermediate data is collected by the monitor process and used to provide the insights on the runtime performance of an algorithm with varying number of requests (Number of processes fixed) as well as varying number of processes(Number of requests fixed).

There are two output files which are generated in user current working directory:
1.	Performance_varyingReqs.csv  : This file contains the performance statistics for the 3 algorithms when run for a fixed number of processes but varying number of requests.
2.	Performance_varyingProcs.csv : This file contains the performance statistics for the 3 algorithms when run for a fixed number of requests but varying number of processes.

In both of the above cases the final output calculated by taking averages over multiple repetition of the same running configuration.
For purpose of demonstrating I have also zipped the output files which were obtained for the following run:
$ python -m da main.da 6 15 3 3 4  



