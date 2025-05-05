#### What does "processing at scale" mean?

- Think Netflix or Hotstar – massive backend systems.
- Not just handling **millions of requests**, they also do **video processing**.
- Ex: A 4K video needs to be available in 2K, HD, etc. – different processing tasks.
- Might also include **ML tasks** – it’s not just about request/response anymore.
- Example: LeetCode runs your code submissions in C++ or Java – **heavy logic** needs processing.

---

#### Why understand how programs execute?

- As a backend engineer, it’s critical to know:
  - How the **programs you write execute on your machine**.
  - How **computers multitask** and run many things internally.
  - How **Node.js processes things** → will dive into **event loop & libuv** later.

---

#### What happens when you "run a program"?

- You write code, save it to HDD/SSD → that’s a **file** = **program**.
- When you run it → becomes a **process**.
- A **process** is a program that’s executing and lives in **RAM**.
- This process contains:
  - Call Stack
  - Heap Memory
  - Text Area (actual instructions)
  - PID (Process ID)
  - Program Counter (tells where we are in instruction)

---

#### What is PCB (Process Control Block)?

- OS allocates resources: CPU time, memory, I/O devices.
- Maintains a **PCB** (a data structure) to track:
  - PID
  - Program Counter
  - State (waiting, running, etc.)
- One PCB per process.
- You can see this in Mac’s **Activity Monitor** or Windows **Task Manager**.

---

#### What is a CPU core?

- A CPU has **multiple cores**.
- Each core = **1 processing unit**.
- Analogy: Amazon has employees → each employee = 1 core, entire workforce = CPU.

---

#### Can a core run multiple processes?

- A core can only execute **1 instruction at a time**.
- So how does it run multiple things? → **Context Switching**.

---

#### What is a Program Counter?

- It’s a **register** that stores address of next instruction to run.
- So like: instructions = [1,2,3], the counter points to 2 while you're running 2.

---

#### How does context switching work?

- CPU runs P1 → then switches to P2 → then P3...
- In 1 second, CPU can execute ~1 billion instructions.
- So it splits time into **small milliseconds** → runs some of P1, switches, etc.
- State of each process saved using **program counter**, so it resumes where it left.
- OS decides which process next using **CPU Scheduling Algorithms**.

---

#### Why is context switching helpful?

- Let’s say P1 is reading from HDD (slow task).
- If you waited for it to finish, other processes are stuck.
- But context switch allows CPU to run others while one waits → this is **multitasking**.

---

#### What happens in multi-core CPUs?

- Same mechanism repeats per core.
- So 2 cores = 2 processes run **in parallel**.
- 4 cores = 4 processes simultaneously.
- If you have more processes than cores → **some cores will context switch**.

---

#### What’s the pigeonhole principle here?

- If you have 8 cores and 9 processes → 1 core handles 2 processes.
- **Only way** to manage this is **context switching**.

---

### 🧠 Summary

Understanding how backend systems process data at scale goes beyond just handling HTTP requests. Real systems like Netflix need CPU-heavy operations like video transcoding. Knowing how programs turn into processes, how the CPU (especially individual cores) executes tasks, and how the OS manages context switching helps build a solid mental model for backend engineering. Concepts like the Program Counter, PCB, and scheduling algorithms come into play. Multi-tasking (on one core) and multi-processing (on multiple cores) are the backbone of modern computing.

---
Perfect, you’re continuing this in the same natural and relatable flow — very real-world, very backend-engineering focused.  
I’ll expand the **Cornell Notes** and keep it matching your updated input.

---

### 📘 Cornell Notes: Backend Engineering – Deepening Understanding: Client-Server, Threads, and Process Management

---

#### **1. Cues (Keywords / Questions)**

| Topic | Questions / Prompts |
|------|----------------------|
| Client-Server Model | How do client and server communicate? |
| Real-world Examples | Facebook likes, bank EMI deductions |
| Server Request Handling | How does a server handle millions of requests? |
| Processes | Why are processes expensive? |
| Threads | How do threads solve server scaling problems? |
| Child Process vs Thread | What's the difference between child process and thread? |
| Concurrency vs Parallelism | How does it actually work under the hood? |
| Heavy Task vs Light Task | How does Linux OS view processes and threads? |

---

#### **2. Notes (Main Content)**

---

### Client and Server
- **Client**: Any process (browser, app) that sends a request.
- **Server**: A process that accepts requests, processes them, and sends back responses.

**Example 1**: Facebook "Like"  
- Mobile app = client  
- Facebook server machines = server  
- When multiple users hit "like", multiple requests are sent, handled separately but concurrently.

**Example 2**: HDFC Loan Deduction  
- HDFC's server requests Axis Bank’s server to deduct EMI every 10th.  
- No user UI involved, but still client-server interaction.

**Important**: Client ≠ UI always. Client = any requester.

---

### Handling Millions of Requests

- **Old way**: For every request → Create new **process** → Process it → Return response.
- **Problem**: 
  - **Process creation** is expensive (memory allocation, CPU time).
  - RAM + CPU resources are heavily used.
  - Not scalable when millions of users are hitting.

- **Hardware Scaling**:  
  - Single-core CPU → Context switching (slow).
  - Octa-core CPU → 8 processes at the same time.
  - But hardware scaling alone is **not enough**.

---

### How Threads Improve Things

- **Thread** = Lightweight process.
- **Analogy**: Full-time employee (process) vs Intern/Contractor (thread).
  
- In a process (P), you can spawn multiple threads (T1, T2, T3).

**Key Properties**:
- Threads share:
  - Heap memory
  - Code section (text)
- Threads have their own:
  - Stack memory
  - Program counter (PC)
- Creation of threads is much cheaper than creation of a new process.

---

### Example: Java Code with 2 Functions

- Single-core CPU: Cannot run f1 and f2 in true parallel.
- Solution: Create two threads (T1 and T2) for f1 and f2.
- Threads will run *concurrently* (fast switching) but **not parallelly** (unless multiple cores).

---

### Deep Dive: How Memory is Handled

| Aspect | Process | Thread |
|--------|---------|--------|
| Heap Memory | New Heap | Shared Heap |
| Stack Memory | New Stack | New Stack |
| Program Counter | New PC | New PC |
| Code Section | New Code Section | Shared Code Section |

- Every thread can point to different parts of code.
- Local variables for threads are different (because they have different stacks).

---

### Child Process vs Thread
- **Child Process**:
  - Fully independent new process.
  - Needs own heap, stack, code section.
- **Thread**:
  - Light and fast.
  - Shares memory and code with parent.

In **Linux**, both processes and threads are called **tasks**:
- Heavy task → Process
- Light task → Thread

(Linux uses special system calls like `fork()` for child processes and `pthread_create()` for threads.)

---

### Final Takeaways

- **Threads** help backend servers handle millions of concurrent requests without overloading CPU and RAM.
- **Threads** allow concurrent execution without creating the cost of full processes.
- Backend scaling = using *thread pools*, *event loops*, *non-blocking IO* rather than just "create a new process for every request."
- Client-server model is everywhere, even when users don’t see any UI.

---

#### **3. Summary**

Backend engineering is about efficiently managing machine-to-machine communication. Threads allow servers to handle massive load without exploding resource usage. Processes are expensive; threads are lightweight. Real-world examples like Facebook and banks show that client-server is just a pattern of communication, not necessarily involving UI. Smart server design uses threads (and thread pools) to achieve concurrency without hardware becoming the bottleneck.

---
