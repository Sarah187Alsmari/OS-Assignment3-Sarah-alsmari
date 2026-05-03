# Assignment 3 - Complete Documentation

**Student Name**: [Sarah alsmari]  
**Student ID**: [445052187]  
**Date Submitted**: [3-May]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [https://drive.google.com/file/d/1Iug_bw-TzX0OloOfhMrrQJWS_z4ds2Ax/view?usp=sharing]

**Video filename**: `[YourStudentID]_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---
## Part 1: Development Log (1 mark)

### Entry 1 - May 1, 2026, 6:00 PM
**What I implemented**: 
I started by setting up the project and updating my student ID in the main class. I reviewed the provided code and identified shared resources such as counters and execution log that require synchronization.

**Challenges encountered**: 
Understanding where race conditions occur was difficult because the program appeared to work normally without synchronization.

**How I solved it**: 
I analyzed the SharedResources class and identified critical sections where multiple threads access the same variables.

**Testing approach**: 
I ran the program before adding synchronization to observe the initial behavior.

**Time spent**: 
1 hour

---

### Entry 2 - May 1, 2026, 8:30 PM
**What I implemented**: 
I added ReentrantLock to protect shared counter variables: contextSwitchCount, completedProcessCount, and totalWaitingTime.

**Challenges encountered**: 
Ensuring that locks are always released even if an exception occurs.

**How I solved it**: 
I used try-finally blocks to guarantee proper release of locks.

**Testing approach**: 
I ran the program multiple times to ensure counters are updated correctly.

**Time spent**: 
1.5 hours

---

### Entry 3 - May 2, 2026, 11:00 AM
**What I implemented**: 
I added a lock for executionLog to prevent concurrent modification.

**Challenges encountered**: 
Understanding that ArrayList is not thread-safe.

**How I solved it**: 
I wrapped executionLog.add() inside a lock.

**Testing approach**: 
I ran the program several times and confirmed no exceptions occurred.

**Time spent**: 
1 hour

---

### Entry 4 - May 2, 2026, 2:00 PM
**What I implemented**: 
I implemented a binary semaphore to control CPU access.

**Challenges encountered**: 
Understanding where to place acquire and release operations.

**How I solved it**: 
I added acquireUninterruptibly() at the beginning of run() and release() in finally.

**Testing approach**: 
I observed that only one process executes at a time.

**Time spent**: 
1 hour

---

### Entry 5 - May 2, 2026, 5:00 PM
**What I implemented**: 
I finalized testing and verified correctness of output.

**Challenges encountered**: 
Ensuring consistent output across multiple runs.

**How I solved it**: 
I ran the program 5 times and compared results.

**Testing approach**: 
Repeated execution and validation.

**Time spent**: 
1 hour

---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions

**Your Answer**:

One race condition occurs in the shared counter variable contextSwitchCount where multiple threads increment the value simultaneously, leading to incorrect results. Another race condition occurs in executionLog because ArrayList is not thread-safe, which may cause inconsistent data or runtime exceptions such as ConcurrentModificationException. These issues arise because threads access shared resources concurrently without synchronization. Locks were used to ensure only one thread accesses these resources at a time. This guarantees correctness and prevents unexpected behavior.

---

### Question 2: Locks vs Semaphores

**Your Answer**:

ReentrantLock provides mutual exclusion by allowing only one thread to enter a critical section. Semaphore controls access using permits and can allow multiple threads depending on the number of permits. In my implementation, I used ReentrantLock to protect shared variables such as counters and executionLog. I used a binary semaphore with one permit to control CPU access, ensuring only one process executes at a time. Locks ensure data consistency while semaphores manage resource access. This combination provides safe and controlled execution.

---

### Question 3: Deadlock Prevention

**Your Answer**:

Deadlock occurs when threads wait indefinitely for resources held by each other. One prevention technique is using try-finally blocks to ensure locks are always released. Another technique is maintaining consistent locking order. In my implementation, I used try-finally blocks for both locks and semaphore operations to guarantee release. This prevents threads from being stuck waiting forever. Proper resource management ensures smooth execution without deadlocks.

---

### Question 4: Lock Granularity Design Decision 

**Your Answer**:

I used one lock (coarse-grained) to protect all three counters. This simplifies the design and ensures consistency. The trade-off is reduced concurrency since only one thread can update counters at a time. Fine-grained locking would allow better concurrency but increase complexity. Since the counters are simple and frequently accessed, one lock is sufficient. However, fine-grained locking would be more efficient in high concurrency scenarios because threads could update different counters simultaneously.

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 
contextSwitchCount, completedProcessCount, totalWaitingTime

**Why they need protection**: 
They are shared among threads and may be modified concurrently.

**Synchronization mechanism used**: 
ReentrantLock

**Code snippet**:
counterLock.lock();
try {
    contextSwitchCount++;
} finally {
    counterLock.unlock();
}

**Justification**: 
The ReentrantLock ensures mutual exclusion, so only one thread can update the shared counters at a time. This prevents race conditions and keeps the final statistics correct.

---
## Critical Section #2: Execution Log
**What resource**:
executionLog ArrayList

**Why it needs protection**:
executionLog needs protection because ArrayList is not thread-safe. If multiple threads add messages at the same time, the list may become inconsistent or cause runtime errors.

**Synchronization mechanism used**:
ReentrantLock

**Code snippet**
:public static void logExecution(String message) {
    // Protect execution log list with a lock
    logLock.lock();
    try {
        executionLog.add(message);
    } finally {
        logLock.unlock();
    }
}

**Justification**:
 Using logLock ensures that only one thread modifies the executionLog at a time. This prevents concurrent modification problems and keeps the log accurate.

---
## Critical Section #3: CPU Semaphore
**Purpose of semaphore**:
The purpose of the semaphore is to control access to CPU execution and ensure that only one process executes at a time.
**Number of permits and why**:
The semaphore uses 1 permit because this is a binary semaphore. It allows only one process thread to enter the CPU execution section at a time.
**Where implemented**:
It is implemented in the run() method and also in runToCompletion().
**Code snippet**:
SharedResources.cpuSemaphore.acquireUninterruptibly();
try {
    // Process execution code
} finally {
    SharedResources.cpuSemaphore.release();
}

**Effect on program behavior**: 
The semaphore makes CPU execution controlled and sequential. It prevents multiple processes from executing the CPU section at the same time, which makes the scheduler output more consistent and easier to verify.

---

## Part 4: Testing and Verification (2 marks)

**Test 1**: Consistency Check
What I tested:
Running the program multiple times to verify consistent results.

Testing procedure:
javac SchedulerSimulationSync.java
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync

Results:
The program ran successfully multiple times. Each run completed all 15 processes, and the final output showed Total Completed Processes: 15. The output also showed Total Context Switches: 30 and Total log entries: 60.
Why synchronization is necessary:
Synchronization is necessary because shared resources such as contextSwitchCount, completedProcessCount, totalWaitingTime, and executionLog are accessed by multiple threads. Without synchronization, updates may be lost or the execution log may become inconsistent. Race conditions may not appear every time, but they can still happen depending on thread scheduling. Locks and semaphores make the program behavior safer and more predictable.
Conclusion:
The consistency test confirms that the synchronization mechanisms work correctly and that the program completes all processes safely.
---
**Test 2**: Exception Testing
What I tested:
Checking for ConcurrentModificationException and other runtime errors related to shared data access.

Testing procedure:
I ran the program multiple times after protecting the executionLog with logLock. I observed the output until the program reached the final statistics section.

Results:
No ConcurrentModificationException occurred. The program completed successfully and printed the execution log summary.

What this proves:
This proves that executionLog is protected properly. The lock prevents multiple threads from modifying the ArrayList at the same time.
---

**Test 3**: Correctness Verification
What I tested:
I verified the final statistics printed by the program, especially the number of completed processes, context switches, and execution log entries.

Expected values:
The number of completed processes should equal the total number of generated processes. In my run, the program generated 15 processes, so the expected completed process count is 15.

Actual values:
The actual output showed Total Completed Processes: 15, Total Context Switches: 30, and Total log entries: 60.

Analysis:
The actual number of completed processes matches the expected value, which means all processes finished successfully. The context switch count is reasonable because several processes required more than one quantum. The execution log count also confirms that logging occurred throughout the simulation.
---

**Test 4**: Different Scenarios
Scenario tested:
I tested the program with the generated scenario based on my student ID, which produced 15 processes and a time quantum of 4000ms.

Purpose:
The purpose was to verify that the program works correctly with multiple processes, different burst times, and repeated queue re-entry for unfinished processes.

Results:
The output showed that processes with burst times greater than the time quantum yielded the CPU and were added back to the ready queue. Processes with remaining time equal to 0 finished successfully. The program ended with all 15 processes completed.

What I learned:
I learned that synchronization is important even when the scheduler appears to execute processes in order. Shared variables still require protection because threads can access them concurrently. I also learned that semaphores are useful for controlling access to limited resources such as CPU execution.
---
## Part 5: Reflection and Learning
What I learned about synchronization:
I learned that synchronization is necessary when multiple threads share the same data. Race conditions can happen when two or more threads update the same variable at the same time. I also learned that ReentrantLock can protect critical sections by allowing only one thread to enter at a time. Semaphore is different because it controls access to a resource using permits. In this assignment, the binary semaphore allowed only one process to execute on the CPU at a time. I also learned that try-finally blocks are very important because they guarantee that locks and semaphores are released. This assignment helped me understand how operating systems manage concurrency and shared resources.
---
## Real-world applications:
**Example 1**:
Banking systems need synchronization when multiple transactions update the same account balance. Without synchronization, two withdrawals or deposits could happen at the same time and produce an incorrect balance.
**Example 2**:
Operating systems need synchronization when managing CPU scheduling, shared memory, files, and I/O devices. Without synchronization, multiple processes could interfere with each other and cause inconsistent system behavior.
---

## How I would explain synchronization to others:
Synchronization is like allowing only one person at a time to use a shared resource. For example, if many students want to use one printer, they must wait in order so that their papers do not mix together. A lock is like a key that one thread holds while using shared data. A semaphore is like a limited number of seats; if there is only one seat, only one thread can enter. In this assignment, locks protected shared variables, while the semaphore controlled access to CPU execution.
---

## Part 6: GitHub Repository Information
**Repository URL**:
[https://github.com/makopt/OS-Assignment3-Starter/compare/main...Sarah187Alsmari:OS-Assignment3-Sarah-alsmari:main]
**Number of commits**:
9
**Commit messages**:
commit for my ID number
TODO #1: Add a ReentrantLock(s) here to protect critical sections
Semaphore
modify incrementContextSwitch
modify incrementCompletedProcess
modify addWaitingTime
modify logExecution
modify run
modify runToCompletion
---

## Summary
**Total time spent on assignment**:
5.5 hours
**Key takeaways**:
Synchronization prevents race conditions in multithreaded programs.
ReentrantLock protects shared data by enforcing mutual exclusion.
Semaphore controls access to limited resources such as CPU execution.

**Most challenging aspect**:
The most challenging aspect was identifying all critical sections in the code and deciding which synchronization mechanism should be used for each one.

**What I'm most proud of**:
I am most proud of completing the synchronization tasks and producing correct output where all 15 processes completed successfully.
---

**End of Documentation**
