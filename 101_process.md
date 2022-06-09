# Operating System - Notes

----------------------------------------- 
# Process


<br/>

## 1. What is a process?
Instance of a program in execution (a program that is currently running).
Process memory structure (process memory is divided into 4 sections):
	- stack : stores local variables
	- heap: dynamic memory allocation
	- data: global & static variables
	- text: compiled program code
	

<p align="center">
  <img src="https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter3/3_01_Process_Memory.jpg">
</p>

> Note: Stack grows downward & heap grows upward
> Question: What Happens When Stack and Heap Collide?
> Answer: https://stackoverflow.com/questions/1334055/what-happens-when-stack-and-heap-collide

<br/>

## 2. Process lifecycle and Process control block.

Process Lifecycle:  Process goes through five stages of its life. 
- When a process is created its in **new** stage. 
- Then it is admitted to ready queue  **ready** stage. 
- From here scheduler will dispatch process to its **running** state.
- While in running phase process could encounter an error or require some resource. If it encounters an error os will send it back to **ready** state.
- In case of I/O or event wait process will be sent to waiting queue **waiting** state.
- After successful execution in running state it would be terminated **terminated** state.

<p align="center">
  <img src="https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter3/3_02_ProcessState.jpg">
</p>

Process Control Block:
- It stores process specification information.
- Contains all necessary information for representing a process.
- Contains state of the process.
- Contains scheduling and memory-management information , ***pointer to process's parent and any of its children***.
- Each process has its own PCB.

<p align="center">
  <img src="https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter3/3_03_PCB.jpg">
</p>


- The process control block in Linux is represented by C structure *task_struct*.
- Example:
	```c
	{
		pid_t pid;  //process identifies
		long state;  //state of the process
		unsigned int time_slice;  // scheduling information
		struct task_struct *parent; // process's parent
		struct list_head children; //process's children
		struct mm_struct *mm;   //address space of this process
	}
	```
<br/>

## 3. What is Process Scheduling?
- Activity of handling the removal of the running process from the CPU and the selection of another process on the basis of scheduling policy(strategy).
- Keeps the CPU busy at all times.
- The process scheduler must implement suitable policies for swapping processes in and out of the CPU.
  
*Following diagram shows what could happen during process(s) execution and how process scheduler deals with it:*
<p align="center">
  <img src="https://res.cloudinary.com/dcjtxt1uo/image/upload/v1654760095/Untitled-2022-05-09-0107_e7udq7.png">
</p>


<br/>

## 4. Different types of Scheduler.

- Long Term Scheduler:
	- Batch system
	- Heavily loaded system
	- Runs frequently
<br/><br/>
- Short Term Scheduler:
	- runs very frequently
	- on the order of 100 millisec.
	- must be very quick to swap processes
<br/><br/>
- Mid Term Scheduler
	- When system load gets high this scheduler will swap one or more processes out of the ready queue for a few seconds.
	- In order to allow smaller faster jobs to finish up quickly

> Note: An efficient scheduling system will select a good process mix of [CPU-bound](https://en.wikipedia.org/wiki/CPU-bound) processes and [I/O bound](https://en.wikipedia.org/wiki/I/O_bound) processes.

<br/>


## 5. What is time Slice?
It is a timeframe for which process is allotted to run in [preemptive](https://www.tutorialspoint.com/preemptive-and-non-preemptive-scheduling) multitasking CPU.

<br/>

## 6. What is Context Switching?
Context  Switching occurs when time-slice expired of one process and other process is to be loaded from ready queue.

- Context switch will be instigated by a timer interrupt which will cause the current process's state to be saved and the new process's state to be restored.
- Saving and restoring states involves saving and restoring all of the registers and program counter(s), as well as the PCB.
- Context switching happens ver very frequently.
- Overhead of doing the switching is just lost CPU time.
- State saves & resores need to be as fast as possoble.
- Some hardware has special instructions to make this fast like a single instruction to save and restore all registers at once.
  
Following are the stages during context switching:
1. process-1 running
2. time slice expired
3. timer interrupt
4. state-save process-1
5. state-restore process-2
6. process-2 running

## 7. Process Creation

- Process can create other processes through appropriate system calls such as fork or spawn.
- Each process is given an integer identifier termed its process identifier (PID).
- Each process also stores its parent's PID.
- On UNIX systems process scheduler is called sched and is given PID 0.
- In unix systems the first process / root process is called init which has pid=1.

<p align="center">
  <img src="https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter3/3_08_ProcessTree.jpg">
</p>


  > Note: The first thing sched process does at system startup time is to launch init, which gives that process PID 1. Init then launches all system daemons and user logins, and becomes the ultimate parent of all other processes.

## 8. What will parent process do after creating a child?
There are two options for the parent process after creating the child:
1. Wait for the child process to terminate before proceeding.The parent makes a [*wait()*](https://man7.org/linux/man-pages/man2/wait.2.html#:~:text=wait()%20and%20waitpid(),pid%20argument%20has%20changed%20state.) system call, for its child which causes the parent process to block until the *wait()* returns.
   
2. Run concurrently with the child, without waiting.
   
3. It is also possible the parent may fork off a number of children without waiting for any of them, then do a little work of its own, and then wait for the children.

## 9. Difference between fork() & exec().
Both process are used to create child process from a parent process.
Difference lies in the possibilities for the address space of the child relative to parent:
1. [fork()](https://man7.org/linux/man-pages/man2/fork.2.html):  The child will be an exact duplicate of the parent, sharing the same program and data segments in memory. Each children will have their own PCB,including program counter,registers,and PID.
   
2. [ exec()](https://man7.org/linux/man-pages/man3/exec.3.html): The child process have a new program loaded into its address space, with all new code and data segments.

## 10. Process Termination
- Process may request their own termination by making the *exit()* system call, which returns an int. This int is passed along to the parent if it is doing a [*wait()*](https://man7.org/linux/man-pages/man2/wait.2.html#:~:text=wait()%20and%20waitpid(),pid%20argument%20has%20changed%20state.).

- wait() returns:
  - 0 in successfil completion
  - some non-zero int : in the event of problems.
  
## 11. What is zombie Process?
- The system can terminate the processes by itself in case it can't deliver required system resources, un handled process interrupt, etc.
- When the child process has no parent process they are termed as orphans.
- *Processes which are trying to terminate but which cannot because their parent is not waiting for them are termed zombies*.
- **On UNIX system zombie process are inherited by init as orpahns and killed off.**

> Note: UNIX [nohup](https://linux.die.net/man/1/nohup) command allows a child to continue executing after its parent has exited.