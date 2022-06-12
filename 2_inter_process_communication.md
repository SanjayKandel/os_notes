# Process Communication

## 1. Types of Processes.
When processes are running concurrently on the system there can be two types of processes:
- Independent processes are those that can neither affect other processes or be affected by other processes.
- Cooperating Processes are those that can affect or be affected by other processes. This is where Inter-Process communication come in.

<br/>

## 2. Need for Cooperating Processes
- Information sharing: Several processes which need access to the same file can share.
- Computation speedup: Problem can be divided into sub tasks to be solved simultaneously.
- Modularity: Breaking down system into cooperating modules.
- Convenience: Single user multi-tasking.

<br/>

## 3. Types of inter-process communication
- Shared Memory
- Message Passing

<br/>

## 4. What is Shared Memory communication?
- In shared memory systems, memory to be shared is initially within the address space of a particulr process. This process needs to make sysem calls in order to make that memory publicly available to other processes.
- Other process which wish to use the shared memory then makes their own system calls to attach the shared memory area onto their address space.

Example: Producer Consumer Problem

<br/>

## 5. Explain Message Passing Systems.
- Message passing model allows multiple processes to read and write data to the message queue without being connected to each other.
<p align="center">
    <img src="https://www.tutorialspoint.com/assets/questions/media/12665/Message%20passing%20modell.PNG">
</p>
- Every message is sent and received via system calls.
- A communication link must be established between the cooperating processes before messages can be sent.

<br/>

## 6. Which mode of communication is best?
| Shared Memory Communication                                     | Message Passing Communication                              |
|-----------------------------------------------------------------|------------------------------------------------------------|
| 1. Once it is set up, no system calls are required.             | 1. Requires system calls for every message trransfer       |
| 2. Faster                                                       | 2. Slower                                                  |
| 3. Complicated to setup                                         | 3. Easy to setup                                           |
| 4. More preferable for information sharing within same computer | 4. Preferable for communication between multiple computers |

<br/>

## 7. Communication in Client-Server Sytems
- Sockets
- Remote Procedure Calls (RPC)
- [For Futher Reading](https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/3_Processes.html)
