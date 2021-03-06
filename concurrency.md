
## Introduction

**History of Concurrency**

- In the past computers had no OS and ran programs ran from beginning to end. Programs had access to all resources of the machine.
- This was bad because:
    - Difficult to write programs for bare metal
    - Running 1 program at a time was inefficient
- An OS allowed us to run more than 1 program at once running individual programs as processes
    - Processes are isolated independently executing programs to which the OS allocates memory, file handles, security creds etc
    - If needed processes could communicate through mechanisms like sockets, files, signal handlers, semaphores, shared memory etc
- In the past each process was a Von Newmann computer - it had resources and executed a set of instructions sequentially
    - Some steps took time and allowed for some asynchrony. While waiting we could do other things and this was the motivation for threads.
    - Threads allow multiple units of control flow to exist within a process.
        - Threads share process wwide resources such as memory and file handles
        - Each thread has its own program counter, stack and variables
        - Threads can also exploit hardware parallelism - that is multiple threads within of same program can be scheduled simultaniously on multiple CPUs
- Most mordern OSs treat threads as the basic units of scheduling.
    - In the absence of explicit coordination threads execute simultaniously and asynchronously.
    - Since threads share memory addresses and space of their owning processes threads will have access to the same vatiables/objects as each other
    - Without explicit synchronization a thread may modify variables another thread is using which can cause problems.

**Benifits of Threads**

- Threads turn asynchronous processes into sequential ones
- Exploiting Multiple Processors:
    - As it becomes harder to scale up clock rates, manufacturers put more cores on a single chip.
    - Since thread is the basic unit of scheduling a program with only 1 thread can run only on 1 processor at a time
        - On a 2 processor system, a single threaded process is giving up access to 50% of the resources available to it. On a 100 processor system it gives up access to 99%
        - Programs with multiple threads can execute simultaniously on multiple processors to achieve better throughput
        - Multithreading can also help on single processor systems. If the processor is idle because of an I/O operation, another thread can do things in the background.
- Simplicity of modelling:
    - A program that performs a single task is simpler to write, easier to test and is less error prone than managing multiple tasks at once.
    - Assigning a thread to each type of task offers the illusion of sequentiality. 
    - A complicated async workflow can be decomposed into synchronous workflows where each thread interact only at specific synchronization points.
- Simplified handling on asynchronous events:
    - Consider the case of a web server. It is much easier to write when each connection is allowed its own thread with synchronous I/O.
    - If your app tries to read asocket when no data is available it blocks until the data is available.
        - In single threaded apps not only does this request stall but all other requests stall
    - We can get around this by using non blocking I/O but that is more complicated/error prone
- More responsive UIs:
    - If gui applications are single threaded, we wither need to frequently poll for input events or everything is executed in a main event loop. Both have problems.
        - If we keep polling, it can be messy and intrusive
        - If we use the single threaded event loop a long operation freezes the UI


**Risks of Threads**

- Safety hazards:
    - In the absence of proper synchronization, the ordering of operations is unpredictable.
    - Let's consider a case where we generate numbers sequentially going from 9 -> 10 -> 11 by doing `value++` and returning it.
        - In a single threaded app this is trivial.
    - Let's say we have 2 threads A and B.
        - If both A and B pick `value = 9` at the same time, both will do `value++` and return 10. Instead of A updating 9->10 and B updating 10->11
        - This is called a race condition specifically a check-then-act race
    - For a multithreaded program to be predictable, access to shared variables must be properly coordinated so threads dont interfere with each other.
    - The reason all variables are not synchronized by default is so the JVM/compiler can make certain optimizations.
        - But as a dev we must identify when it is safe to allow such conditions.
- Liveness hazards:
    - Liveness means something good may eventually happen.
    - An example of a liveness failure in single threaded applications is when we have `A->Loop->B`. If loop is infinite, B never happens.
    - Multithreading adds additional failure cases. If thread A is waiting for a resource but B never releases it A will wait forever.
    - Liveness failures can be elusive because they depend on relative timing of events and may not manifest themselves during developmengt or testing.
- Performance hazards:
    - Most of the time using threads results in a net performance gain but this is not always the case
    - With more threads there are more context switches
        - A context switch is when the scheduler temporarily suspends the active thread so another thread can run.
    - This has performance costs associated with saving/restoring execution context, loss of locality, time spent scheduling threads instead of running them etc

------------------

## Thread Safety
