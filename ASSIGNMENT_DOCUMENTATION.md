# Assignment 3 - Complete Documentation

**Student Name**: [shuruq saud]  
**Student ID**: [445052153]  
**Date Submitted**: [7.5.2026]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [Paste your personal Gmail Google Drive link here]

**Video filename**: `[YourStudentID]_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - May 5, 2026, 7:30 PM

What I implemented:
I started by setting up the project and updating the student ID in the SchedulerSimulationSync.java file. I also reviewed the assignment requirements and identified the shared resources that may cause race conditions.

Challenges encountered:
At first, I had difficulty understanding which variables needed synchronization and how race conditions happen in multithreaded programs.

How I solved it:
I reviewed the shared variables carefully and identified the critical sections in the code such as counters and execution logs.

Testing approach:
I compiled and ran the program to make sure the project setup worked correctly before adding synchronization mechanisms.

Time spent:
1 hour

---

### Entry 2 - May 6, 2026, 5:00 PM

What I implemented:
I added ReentrantLock to protect shared counter variables including contextSwitchCount, completedProcessCount, and totalWaitingTime.

Challenges encountered:
I initially forgot to release the lock correctly, which could lead to deadlock problems.

How I solved it:
I used try-finally blocks to guarantee that unlock() is always executed even if an error occurs.

Testing approach:
I executed the program several times and verified that the counters displayed consistent and correct values.

Time spent:
2 hours
---

### Entry 3 - May 7, 2026, 8:15 PM

What I implemented:
I added synchronization for the execution log using ReentrantLock and implemented a binary Semaphore to control CPU access.

Challenges encountered:
I faced issues understanding how Semaphore controls thread access to shared resources.

How I solved it:
I studied the Semaphore behavior and used a single permit to ensure only one process can access the CPU section at a time.

Testing approach:
I ran the simulation multiple times and checked that the execution log worked correctly without errors and that processes executed consistently.

Time spent:
2 hours
---

### Entry 4 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

### Entry 5 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:
One race condition in the original code happens with the shared counter variable `contextSwitchCount`. This variable is updated every time the scheduler switches to another process, for example: `contextSwitchCount++`. If multiple threads update this counter at the same time, one update may overwrite another update. This can cause the final number of context switches to be lower or incorrect.

A second race condition happens with the shared `executionLog` ArrayList. The program adds log messages during process execution, for example: `executionLog.add(process.getName() + " completed execution");`. Since `ArrayList` is not thread-safe, concurrent access by multiple threads can corrupt the list or cause inconsistent log entries. It could also lead to errors such as `ConcurrentModificationException` or missing log messages.
[Your answer here - 4-6 sentences with code examples]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:
ReentrantLock is used to protect critical sections of code and allows only one thread to access shared data at a time. In my code, I used ReentrantLock to protect shared counter variables such as `contextSwitchCount`, `completedProcessCount`, and `totalWaitingTime`. I also used it for the `executionLog` ArrayList because multiple threads may try to modify it simultaneously.

Semaphore is used to control access to a shared resource by limiting the number of threads that can enter a section at the same time. In my implementation, I used a binary Semaphore with one permit to control CPU access. This ensures that only one process can execute in the CPU section at a time, which simulates real CPU scheduling behavior and prevents concurrent execution conflicts.
[Your answer here - explain your implementation choices]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:
Deadlock is a situation where two or more threads are waiting for each other to release resources, causing the program to stop progressing. This usually happens when threads hold locks and wait indefinitely for another lock.

One deadlock prevention technique is using `try-finally` blocks. In my code, I used `try-finally` whenever I used `ReentrantLock` or `Semaphore`. This guarantees that `unlock()` and `release()` are always executed even if an exception occurs.

Another prevention technique is avoiding unnecessary nested locks and keeping lock usage simple. In my implementation, I used separate locks for counters and execution logs and avoided holding multiple locks for a long time. This reduces the possibility of circular waiting between threads and helps prevent deadlocks.
[Your answer here - reference try-finally blocks, lock ordering, etc.]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:
For Task 1, I used one lock for the three counters, which is a coarse-grained locking approach. I made this choice because it is simpler to implement, easier to read, and reduces the chance of making synchronization mistakes. The trade-off is that coarse-grained locking can reduce concurrency because only one thread can update any counter at a time. Fine-grained locking uses a separate lock for each counter, which allows different threads to update different counters at the same time. This improves concurrency but makes the code more complex and harder to manage. Since the three counters are independent, fine-grained locking would provide better concurrency because each counter could be protected separately. However, for this assignment, one lock is acceptable because the counter updates are short and the design is easier to verify.
[Your answer here - explain coarse-grained vs fine-grained locking, independence of counters, concurrency implications. Show understanding of when to use each approach. 5-8 sentences expected.]

---

### Critical Section #1: Counter Variables

**Which variables**:  
`contextSwitchCount`, `completedProcessCount`, and `totalWaitingTime`

**Why they need protection**:  
These variables are shared between threads and may be updated during process execution. Without synchronization, two threads could update the same counter at the same time, causing lost updates or incorrect final statistics.

**Synchronization mechanism used**:  
I used `ReentrantLock` to make sure only one thread can update the counter variables at a time.

**Code snippet**:
```java
private static final ReentrantLock counterLock = new ReentrantLock();

public static void incrementContextSwitchCount() {
    counterLock.lock();
    try {
        contextSwitchCount++;
    } finally {
        counterLock.unlock();
    }
}
// Paste your implementation here
```

**Justification**: 

---

### Critical Section #2: Execution Log

**What resource**: 

**Why it needs protection**: 

**Synchronization mechanism used**: 

**Code snippet**:
```java
// Paste your implementation here
```

**Justification**: 

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 

**Number of permits and why**: 

**Where implemented**: 

**Code snippet**:
```java
// Paste your implementation here
```

**Effect on program behavior**: 

---

## Part 4: Testing and Verification

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**:
```bash
javac SchedulerSimulationSync.java
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync

### Test 4: Different Scenarios
**Scenario tested**: [e.g., different time quantum, more processes, etc.]

**Purpose**: 

**Results**: 

**What I learned**: 

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[6-8 sentences about key concepts, challenges, insights]

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 

**Example 2**: 

---

### How I would explain synchronization to others:

[Explain to someone who just finished Assignment 1 - use simple terms and analogies]

---

## Part 6: GitHub Repository Information

**Repository URL**: 

**Number of commits**: 

**Commit messages**: 
1. 
2. 
3. 
4. 

---

## Summary

**Total time spent on assignment**: 

**Key takeaways**: 
1. 
2. 
3. 

**Most challenging aspect**: 

**What I'm most proud of**: 

---

**End of Documentation**
