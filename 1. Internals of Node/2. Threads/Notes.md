 **Topic: Processing at Scale & CPU Execution** |
 ---

### **Cue/Self-Test Column (Key Points/Questions)**  
1. **What is "processing at scale"?**  
   - Examples of systems needing heavy processing.  
   - Why backend systems need both high requests and CPU-intensive tasks.  
2. **Program vs. Process**  
   - What happens when a program runs?  
   - Components of a process (call stack, heap, text area, PID, program counter).  
3. **Process Control Block (PCB)**  
   - What details does PCB store?  
   - How does the OS use PCB?  
4. **CPU Cores**  
   - What is a CPU core? (Amazon workforce analogy)  
   - How does a single-core CPU execute processes?  
5. **Context Switching**  
   - How does a single-core CPU handle multitasking?  
   - Why is context switching necessary?  
6. **Multi-core vs. Multi-tasking**  
   - What happens with 8 cores and 9 processes?  
   - Difference between multiprocessing and multitasking.  

---

### **Note-Taking Area (Detailed Content)**  

#### **1. Processing at Scale**  
- **Examples**:  
  - **Netflix/Hotstar**: Process videos (e.g., 4K → 2K, machine learning tasks).  
  - **LeetCode**: Executes user-submitted code (e.g., C++ solutions) on servers. Even simple test cases (text files) require heavy processing.  
- **Key Point**: Backend systems handle **both** high traffic (millions of requests) **and** CPU-heavy tasks (video encoding, ML, code execution).  

---

#### **2. Program vs. Process**  
- **Program**:  
  - Static file saved on HDD/SSD (e.g., `script.js`).  
- **Process**:  
  - **Program under execution** loaded into RAM.  
  - **Components**:  
    - **Call stack**: Tracks function calls.  
    - **Heap**: Dynamic memory allocation.  
    - **Text area**: Stores machine code instructions.  
    - **PID**: Unique process ID.  
    - **Program counter**: Address of the next instruction to execute.  
- **Why different terms?**  
  - A process is the OS-managed entity with allocated resources (CPU, memory, I/O).  

---

#### **3. Process Control Block (PCB)**  
- **PCB**: Data structure created by the OS for **each process**.  
  - **Stores**:  
    - **PID**, **program counter**, **state** (running/ready/blocked).  
    - Memory allocation details, CPU registers, I/O status.  
  - **Purpose**:  
    - Track process details for **context switching**.  
    - OS allocates CPU time, memory, and devices via PCB.  

---

#### **4. CPU Cores**  
- **Core**: Single computing unit (analogy: Amazon employee = core; workforce = CPU).  
- **Single-core execution**:  
  - At any instant, a core runs **one instruction** from **one process** (machine code level).  
- **Program Counter**:  
  - CPU register storing the **next instruction address** (e.g., after step 1, points to step 2).  

---

#### **5. Context Switching**  
- **How it works**:  
  1. OS splits CPU time into slices (e.g., 10ms).  
  2. Runs Process P1 → saves its PCB → switches to P2 → repeats.  
- **Why needed?**  
  - Avoid CPU idling during **blocking tasks** (e.g., HDD read, user input).  
  - Example: If P1 waits for an HDD read, CPU switches to P2 instead of waiting.  
- **CPU Scheduling Algorithms**:  
  - Rules (e.g., Round Robin) decide which process runs next.  

---

#### **6. Multi-core vs. Multi-tasking**  
- **Multi-core (Multiprocessing)**:  
  - Parallel execution: 8 cores → 8 processes run simultaneously.  
  - **Pigeonhole Principle**: 9 processes on 8 cores → 1 core handles 2 processes via **context switching**.  
- **Multi-tasking (Single-core)**:  
  - Simulates parallelism via rapid context switches (~1 billion instructions/sec).  

---

### **Summary**  
1. **Processing at Scale**: Systems like Netflix/LeetCode handle **high traffic** and **CPU-heavy tasks** (video encoding, code execution).  
2. **Process vs. Program**: A process is a running program with resources (RAM, CPU) managed by the OS via **PCB**.  
3. **Single-Core Execution**: Runs one instruction at a time. **Context switching** prevents CPU idling during blocking tasks.  
4. **Multi-core**: Parallel processing + context switching for extra processes (pigeonhole principle).  
5. **Tools**: Monitor processes via Activity Monitor (Mac) or Task Manager (Windows).  

---

### **Self-Test Questions**  
1. Why does Netflix need both high request handling and CPU-intensive processing?  
2. What is stored in a process’s **text area**?  
3. How does the PCB help the OS manage processes?  
4. Explain the Amazon workforce analogy for CPU cores.  
5. What happens if 9 processes run on an 8-core CPU?  

---
**Cornell Notes Template**  
**Topic: Client-Server Architecture, Scalability, & Threads** | **Date: [Add Date]**  

---

### **Cue/Self-Test Column (Key Points/Questions)**  
1. **Client-Server Architecture**  
   - What defines a "client" and "server"?  
   - Example: Facebook likes vs. HDFC-Axis Bank transactions.  
2. **Scalability Issues with Processes**  
   - Why is creating a new process for each request inefficient?  
3. **Threads as Lightweight Solutions**  
   - How do threads solve scalability challenges?  
   - What resources do threads share vs. processes?  
4. **Hardware vs. Software Threads**  
   - What is the difference between Intel’s "performance" and "efficient" cores?  
   - How do software threads relate to CPU cores?  
5. **Threads vs. Child Processes**  
   - Why should servers prefer threads over processes?  

---

### **Note-Taking Area (Detailed Content)**  

#### **1. Client-Server Architecture**  
- **Client**:  
  - Any process requesting a task (e.g., Facebook app on a phone, HDFC’s automated loan deduction system).  
  - **No UI required**: Clients can be backend systems (e.g., HDFC’s process requesting Axis Bank to deduct funds).  
- **Server**:  
  - A process that accepts requests, processes them, and sends responses (e.g., Facebook’s servers handling likes, Axis Bank deducting funds).  
- **Key Challenge**:  
  - Handling **millions of concurrent requests** (e.g., Facebook likes, bank transactions).  

---

#### **2. Scalability Issues with Processes**  
- **Traditional Approach**:  
  - Servers created a **new process** for **each request**.  
  - **Problems**:  
    - **Resource Overhead**: Each process needs its own RAM, PCB, and CPU registers.  
    - **Hardware Limits**: Even with 8 cores, scaling to millions of requests is impossible.  
- **Example**:  
  - Single-core CPUs use **context switching** to juggle processes, but creating/terminating processes is slow and resource-heavy.  

---

#### **3. Threads as Lightweight Solutions**  
- **Definition**:  
  - Threads are subunits of a process that share resources (heap, text area) but have their own stack and program counter.  
  - **Analogy**: Full-time employees (processes) vs. interns (threads). Interns share office resources but handle smaller tasks.  
- **Why Threads Are Efficient**:  
  - **Shared Resources**: Threads reuse the parent process’s heap, global variables, and code (text area).  
  - **Faster Creation**: No need to allocate new memory or set up a full PCB.  
- **Example**:  
  - A Java process with functions `f1()` and `f2()` spawns threads `t1` and `t2` to run them **concurrently** (not parallel on single-core CPUs).  

---

#### **4. Hardware vs. Software Threads**  
- **Hardware Threads (CPU Cores)**:  
  - **Intel Example**:  
    - **Performance Core** = 2 logical threads (handles heavier tasks).  
    - **Efficient Core** = 1 logical thread (handles lighter tasks).  
  - **Limitation**: A core can only run **one software thread** at a time.  
- **Software Threads**:  
  - Managed by the OS (e.g., Java/Python threads).  
  - **Concurrency vs. Parallelism**:  
    - **Single-core**: Threads run concurrently via context switching.  
    - **Multi-core**: Threads run in parallel.  

---

#### **5. Threads vs. Child Processes**  
- **Child Process**:  
  - A full-fledged process with its own memory, PCB, and resources.  
  - **Example**: Linux uses `fork()` to create child processes.  
- **Threads**:  
  - Lightweight, share parent process resources.  
  - **Why Servers Prefer Threads**:  
    - Lower memory usage, faster creation/termination.  
    - Ideal for handling millions of requests (e.g., Facebook likes).  

---

### **Summary**  
1. **Client-Server Architecture**: Clients (UI or backend systems) send requests to servers (e.g., Facebook, Axis Bank).  
2. **Scalability**: Processes are inefficient for handling millions of requests due to high resource overhead.  
3. **Threads**: Lightweight, share resources (heap/text), and enable concurrency.  
4. **Hardware vs. Software**: CPU cores limit parallelism; software threads enable scalable concurrency.  
5. **Threads > Processes**: Servers use threads for efficiency (lower memory, faster execution).  

---

### **Self-Test Questions**  
1. How does the HDFC-Axis Bank example illustrate client-server architecture without a UI?  
2. Why is creating a new process for each request impractical for Facebook?  
3. What resources do threads share with their parent process?  
4. Explain the analogy of "interns vs. full-time employees" for threads vs. processes.  
5. Why do servers prefer threads over child processes?  

---
