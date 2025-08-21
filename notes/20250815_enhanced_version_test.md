# Operating Systems: Scheduling - Week 3 Lecture Notes

## Overview
This lecture covers the fundamental concepts of process scheduling in operating systems. We'll explore how operating systems manage multiple processes to optimize system performance, using various scheduling algorithms and metrics to determine "good" versus "bad" scheduling decisions. The lecture synthesizes both theoretical foundations from slides with practical insights from classroom discussion.

---

## Scheduling Fundamentals

### Scheduler vs Dispatcher
**Scheduler**: Determines *when* a process should run (conceptual decision-making).  
**Dispatcher**: Handles the *how* of switching between processes (mechanical execution).

> "The scheduler is like deciding which assignment to do first, while the dispatcher is actually doing it—saving PCBs, switching modes, etc."

*Instructor Emphasis*: "If you don't know what metric means at this point in your university careers, I feel very bad for you."  
*Practical Example*: The hospital operating room analogy (discussed later) illustrates how scheduling decisions impact outcomes.

### Process States and Scheduling Context
Processes move between three fundamental states:
- **Running**: Currently executing on the CPU
- **Ready**: Waiting to be scheduled but not blocked
- **Blocked**: Waiting for I/O operations or other resources

> "Imagine you're working on assignments. You can only work on one assignment at a time (running), others are ready but waiting, and some might be blocked if they need feedback from an instructor."

---

## Scheduling Metrics: What Makes Good Scheduling?

### Key Metrics Explained
| Metric | Definition | Why It Matters |
|--------|------------|----------------|
| **Turnaround Time** | Completion time - Arrival time | Total time a job spends in the system (from arrival to completion) |
| **Response Time** | Start time - Arrival time | How quickly a process gets its first response after arriving |
| **Wait Time** | Time spent waiting for CPU (excluding I/O wait) | Time spent ready but not running |
| **Throughput** | Number of jobs completed per unit time | System efficiency at processing workloads |
| **Resource Utilization** | Percentage of time CPU is busy | Avoiding idle resources |
| **Overhead** | Cost of context switching between processes | Too many switches reduce effective processing time |
| **Fairness** | Equal treatment across all processes | Preventing starvation (some jobs never running) |

*Instructor Insight*: "When you're doing assignments at university, does anyone do this where they're like, 'I've got a tutorial that'll take me half an hour to do. But I'll do my large assignment first'? No one operates this way."

### Hospital Operating Room Analogy
> "Imagine running a hospital with one operating room (CPU). You have patients needing surgery (processes) arriving at different times. What metrics would you care about? Less dead people = good scheduling, but we need measurable criteria like turnaround time (time from arrival to finish), response time (how quickly treatment starts), and throughput (number of successful surgeries per hour)."

*Student Question*: "How do we determine the best way to use the operating room?"  
*Instructor Response*: "We measure metrics: turnaround time, response time, wait time, throughput, resource utilization, overhead, and fairness."

---

## Scheduling Algorithms

### Gantt Charts & Basic Assumptions
Gantt charts visually represent process scheduling over time. We'll use these to compare algorithms.

**Scheduling Assumptions (for simplicity in examples):**
1. Jobs run for a fixed duration
2. All jobs arrive simultaneously
3. All jobs are CPU-only (no I/O)
4. Run-time of each job is known

> "These assumptions aren't true in real systems, but they help us build and compare algorithms before adding complexity."

---

### First Come, First Serve (FCFS)

**Algorithm**: Processes run in the order they arrive.

#### Example Calculation
| Job | Arrival Time | Burst Time | Completion Time | Turnaround Time |
|-----|--------------|------------|-----------------|----------------|
| A   | 0            | 5          | 5               | 5              |
| B   | 0            | 5          | 10              | 10             |
| C   | 0            | 5          | 15              | 15             |

**Average Turnaround Time**: (5 + 10 + 15) / 3 = **10**

#### Poorer Example
| Job | Arrival Time | Burst Time | Completion Time | Turnaround Time |
|-----|--------------|------------|-----------------|----------------|
| A   | 0            | 20         | 20              | 20             |
| B   | 0            | 4          | 24              | 24             |
| C   | 0            | 4          | 28              | 28             |

**Average Turnaround Time**: (20 + 24 + 28) / 3 = **24**

*Instructor Insight*: "This is the insane way of operating—doing a large assignment before a tutorial. The only reason to use FCFS is because it's simple to implement."

#### Key Problems
- **Convoy Effect**: Long processes block short ones (like cars waiting behind a slow truck on highway)
- **Starvation**: Short jobs can be delayed indefinitely by long jobs

> "Imagine you're driving and see a huge truck ahead. All the little cars are stuck behind it—this is the convoy effect."

---

### Shortest Job First (SJF)

**Algorithm**: Run shortest job next.

#### Example Calculation
| Job | Arrival Time | Burst Time | Completion Time | Turnaround Time |
|-----|--------------|------------|-----------------|----------------|
| B   | 0            | 4          | 4               | 4              |
| C   | 0            | 4          | 8               | 8              |
| A   | 0            | 20         | 28              | 28             |

**Average Turnaround Time**: (4 + 8 + 28) / 3 = **16.6**

*Instructor Insight*: "Shortest job first is the solution to all our problems—until tasks arrive at different times."

#### Non-Preemptive vs Preemptive
- **Non-preemptive**: Once started, a process runs until completion (like FCFS)
- **Preemptive**: Can interrupt and switch processes mid-execution

> "If task B arrives while A is running but has shorter time remaining, we should preempt A to run B first."

---

### Shortest Time to Completion First (STCF)

**Algorithm**: Preemptive version of SJF—always run the job that will finish soonest.

#### Example Calculation
| Job | Arrival Time | Burst Time | Completion Time | Turnaround Time | Response Time |
|-----|--------------|------------|-----------------|----------------|---------------|
| A   | 0            | 10         | 10              | 10             | 0             |
| B   | 2            | 10         | 18              | 16             | 8             |
| C   | 4            | 10         | 26              | 22             | 16            |

**Average Response Time**: (0 + 8 + 16) / 3 = **8**

*Instructor Insight*: "STCF is essentially 'earliest deadline first' in real-time systems. It's about getting things done as quickly as possible."

---

### Round Robin (RR)

**Algorithm**: Each process gets a fixed time slice before being preempted.

#### Example Calculation
| Job | Arrival Time | Burst Time | Completion Time | Turnaround Time | Response Time |
|-----|--------------|------------|-----------------|----------------|---------------|
| A   | 0            | 10         | 26              | 26             | 0             |
| B   | 2            | 10         | 26              | 24             | 0             |
| C   | 4            | 10         | 26              | 22             | 0             |

**Average Response Time**: (0 + 0 + 0) / 3 = **0**

*Instructor Insight*: "Round Robin is fairer but can have poor response times if time slices are too long. If you switch too often, overhead increases; if not enough, response times suffer."

> "Imagine a classroom where each student gets exactly one minute to speak before the next person talks—fair for everyone, but it takes longer for anyone to finish their point."

---

## Advanced Scheduling Concepts

### I/O Blocking and CPU Utilization
Processes often alternate between CPU time and I/O waits. We should avoid wasting CPU when a process is waiting for I/O.

#### Example of Efficient Scheduling
```
Time: 0-2   | Task A (CPU)
Time: 2-6   | Task A (I/O) → Switch to Task B
Time: 6-8   | Task B (CPU)
Time: 8-10  | Task B (I/O) → Switch to Task C
Time: 10-12 | Task C (CPU)
```

*Instructor Insight*: "Don't waste CPU time. When a process is waiting for I/O, switch to another ready process."

> "Interactive jobs like keyboard input need quick response times; batch jobs can wait longer. This is why we have different scheduling strategies for different job types."

---

### Multi-Level Feedback Queue (MLFQ)

**Problem**: How to handle processes of varying lengths and priorities?

#### MLFQ Principles
1. Run high-priority jobs first (round robin within priority levels)
2. All new jobs start at highest priority
3. If a process runs too long, its priority drops
4. Periodically reset all priorities

*Instructor Insight*: "If you let a process decide how long it will run, it would say '0 seconds'—it's lying to get more CPU time."

#### Priority Dynamics (Evil Strategy)
> "How can we be evil? If your job runs for just under the allotted time before priority drops, then sleeps briefly and restarts, you never lose priority. This is called 'leaving a tiny gap' in scheduling."

*Instructor Emphasis*: "If you use a full allotment (not partial), your priority drops. But if you run 90% of your allotment, your priority won't fall—this is how to stay at top priority."

#### MLFQ Design Decisions
- How many queues?
- How long should an allotment be?
- Are allotments the same for each queue level?
- How often to refresh priorities?

*Instructor Insight*: "These are design decisions. You can't calculate them perfectly—you experiment and see what works best."

---

### Lottery Scheduling

**Concept**: Processes get tickets proportional to priority; a random ticket is chosen.

> "Imagine you have 100 tickets for high-priority tasks, while low-priority gets just one—your chance of being selected is proportional to your tickets."

*Instructor Insight*: "This leads to interesting questions: Could processes trade tickets? What about 'ticket inflation' where old tickets become more valuable over time?"

---

### Completely Fair Scheduler (CFS)

**Premise**: All tasks get a fair share based on their weight.

#### Key Features
- **vruntime**: Measures how long a task has run
- Smallest vruntime gets highest priority
- Each task runs for ~target latency period
- No starvation—long-running jobs eventually complete

*Instructor Insight*: "CFS works using a red-black tree to efficiently find the smallest vruntime (O(log n) time)."

#### Example: Video Rendering vs Word Processor
| Task | Type | Behavior |
|------|------|----------|
| Video Rendering | CPU-intensive, non-interactive | Accumulates high vruntime; runs in background |
| Word Processor | Interactive | Accumulates low vruntime; gets priority when user inputs |

> "When you click a button on your word processor, it has accumulated little vruntime and gets immediate execution."

#### Nice Values
*Instructor Insight*: "The 'nice' value is like a soft priority. Lower nice = more CPU time (e.g., `nice -5` gives higher priority)."

```bash
# Example: Give process 1234 a lower nice value (higher priority)
nice -n -5 1234
```

---

## Multi-Core Considerations

*Instructor Insight*: "Linux uses one red-black tree per core for CFS. But distributing tasks across cores fairly is challenging—like choosing the shortest queue at an airport."

> "Imagine waiting in line at an airport with six queues. You pick a queue, but it's slow while others are empty. How do you switch to the faster queue without wasting time?"

---

## Summary of Key Scheduling Algorithms

| Algorithm | Best For | Pros | Cons |
|-----------|----------|------|------|
| **FCFS** | Simple systems | Very easy to implement | Convoy effect, starvation risk |
| **SJF/SJF Preemptive (STCF)** | Minimizing average turnaround time | Reduces wait times for short jobs | Requires knowing burst times in advance |
| **Round Robin** | Interactive systems | Fairness, good response times | Overhead from frequent context switches |
| **MLFQ** | Mixed workloads (interactive + batch) | Handles varying job lengths well | Complex to tune parameters |
| **CFS** | Modern Linux systems | No starvation, fair distribution | Requires efficient data structure |

*Instructor Insight*: "The completely fair scheduler is used in Linux since 2007. It's not perfect but works remarkably well."

---

## Key Takeaways

1. Scheduling decisions impact critical metrics: turnaround time, response time, throughput, and fairness.
2. FCFS is simple but inefficient for mixed workloads due to convoy effect.
3. SJF/STCF minimizes average turnaround time when burst times are known.
4. RR provides fairness at the cost of potential overhead.
5. MLFQ dynamically adjusts priorities based on job behavior.
6. CFS uses vruntime and red-black trees to ensure fair CPU distribution.

---

## Study Questions

1. Why is FCFS considered "simple but bad" for scheduling?
2. Calculate turnaround time, response time, and wait time for a process that arrives at 3ms with burst time of 8ms completing at 15ms.
3. Explain the convoy effect using an analogy beyond traffic.
4. How does STCF differ from SJF? Why is it called "shortest time to completion first"?
5. What happens if you set a process's nice value to -20 in Linux?
6. Describe how CFS ensures no starvation occurs for long-running jobs.
7. Explain why MLFQ needs periodic priority resets.
8. How would you design an algorithm that minimizes wait time while maintaining fairness?
9. Why is it impossible to know exact burst times in real systems? What do we assume instead?
10. Compare the overhead of FCFS versus Round Robin with a 5ms time slice.
11. Describe how I/O blocking affects scheduling decisions.
12. Explain why MLFQ prioritizes interactive jobs over batch jobs.
13. How does CFS use red-black trees to improve efficiency?
14. What is "ticket inflation" in lottery scheduling, and why might it be problematic?
15. Why would a process with high priority (low nice value) get more CPU time than others?

---

## Practical Exercise

Create a Gantt chart for the following processes using FCFS, SJF, and RR (time slice = 3):

| Process | Arrival Time | Burst Time |
|---------|--------------|------------|
| P1      | 0            | 8          |
| P2      | 1            | 4          |
| P3      | 2            | 9          |

Calculate the average turnaround time for each algorithm and compare results.