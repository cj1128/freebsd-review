<div align="center">
  <h1>
    <a href="https://www.amazon.com/Design-Implementation-FreeBSD-Operating-System/dp/0321968972">
      The Design and Implementation of the FreeBSD Operating System (2nd Edition)
    </a>
    Review
</h1>

  <img width="600px" src="http://ww1.sinaimg.cn/large/9b85365dgy1fst9fkp82sj20sg163dkk">
</div>

## Chapter 1: History and Goals

## Chapter 2: Design Overview of FreeBSD

### Exercises

- 2.1: How does a user process request a service from the kernel?

  by *system calls*.

- 2.2: How are data transferred between a process and the kernel? What alternatives are available?

  By copying from the process address space into a buffer in kernel.

  For large quantities of data, we can use `mmap` system call to remap the process memory into the kernel.

  `mmap` is not supported between two processes running on different machines, at that situation, we can use `sendfile` system call.

- 2.3: How does a process access an I/O stream? List three types of I/O streams.

  Process uses `descriptors` to access I/O streams. Descriptors are obtained by

  1. opening or creating an object, or
  2. inheriting from the parent process

  In FreeBSD, there are 7 types of I/O streams: `files`, `pipes`, `fifos`, `sockets`, `POSIX IPC`, `event queues`, and `processes`.

- 2.4: What are the four steps in the lifecycle of a process?

  `fork`, `execve`, `exit` and `wait`.

- 2.5: Why are process groups provided in FreeBSD?

  Process groups are used to control access to terminals and to provide a means of distributing signals to collections of related processes.

- 2.6: Describe four machine-dependent functions of the kernel.

  - Low-level system-startup actions
  - Trap and fault handling
  - Configuration and initialization of hardware devices
  - Low-level manipulation of the run-time context of a process

- 2.7: Describe the difference between an absolute and a relative pathname.

  A pathname beginning with a slash is an absolute pathname, a pathname that doesn't begin with a slash is a relative pathname.

- 2.8: Give three reasons why the `mkdir` system call was added to 4.2BSD.

  - the operation could be made atomic.
  - when a networked filesystem is used
  - when supporting non-UNIX filesystems

- 2.9: Define *scatter-gather I/O*. Why is it useful?

  Scatter-gather I/O allows a single read to be placed in several different buffers and several different buffers can be written in a single atomic write.

  This facility allows buffers in different parts of a process address space to be written atomically, without the need to copy them to a single contiguous buffer.

  It's convenient to be able to read a single request into several different buffers.

- 2.10: What is the difference between a pipe and a socket?

  1. Pipes require a common parent process to set up a communications channel, but connections between socks can be set up by two unrelated processes.
  2. Pipes are unidirectional, but socks are bidirectional.

- 2.11: Descirbe how to create a group of processes in a pipeline.

  The shell forks a process for each stage of the pipeline, and then putting all those processes into a separate process group.

- 2.12: List the three system calls that were required to create a new directory `foo` in the current directory before the addition of the `mkdir` system call.

  Based on [Mkdir Wiki](https://en.wikipedia.org/wiki/Mkdir), before the `mkdir` system call, kernel first uses `mknod` to create the directory, then uses two `link` system calls to link `.` and `..`.

- 2.13: Explain the difference between interprocess communication and networking.

  Networking is one of many ways for interprocess communication, it enables two processes located on different machines communicate.

  Besides netowrking, there are other ways for interprocess communication which only work for local processes, such as `pipes`, `fifos`, `semaphores`, `message queues` and `shared memory`.

## Chapter 3: Kernel Services

### Exercises

- 3.1: Describe three types of system activity.

  *System activity* is an entry into the kernel. they can be categorized according to the event or action that initiates them:

  + system calls
  + hardware interrupts
  + hardware traps
  + software-initiated interrupts
  - software-initiated traps

- 3.2: When can a routine executing in the top half of the kernel be preempted? When can it be interrupted?

  The FreeBSD kernel is rarely preempted to run another user process while executing in the top half of the kernel. Although, it will explicitly give up the processer if it must wait for an event or for a shared resource.

  Its execution may be interrupted, however, by the need to run a real-time process or by interruptes for the bottom half of the kernel.

- 3.3: Why are routines executing in the bottom half of the kernel precluded from using information located in the current user process?

  Activities in the bottom half of the kernel are **synchronous** with respect to the interrupt source but are **asynchronous**, with respect to the top half, and the software can't depend on having a specific process running when an interrupt occurs. 

  Thus, the state information for the process that initiated the activity is not available.

- 3.4: Why does the system defer as much work as possible from high-priority interrupts to lower-priority software-interrupt processes?
  
  High-priority interrupts are critical operations, they need to run as quick as possible. So the system moves as much work as possible from them to lower-priority processes.
