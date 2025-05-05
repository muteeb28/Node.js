### **1. Introduction to Node.js**  
#### **What is Node.js?**  
- **Analogy**: A "Swiss Army knife" for JavaScript – adds tools (OS access, servers) to JS, which is otherwise limited to browsers.  
- **Runtime Environment**:  
  - **Core Purpose**: Lets JS interact with the OS (files, networks, RAM).  
  - **Example**:  
    ```javascript
    const fs = require('fs'); 
    fs.readFile('file.txt', (err, data) => { 
      // Node.js unlocks file reading!
    });
    ```  

#### **What Node.js is Not**  
- ❌ **Not a Framework**: It’s a runtime (like a "translator" for JS to talk to the OS).  
- ❌ **Not Single-Threaded Everywhere**: Uses worker threads (via `libuv`) for heavy tasks (e.g., file compression).  

#### **Internals of Node.js**  
- **Architecture Flow**:  
  ```plaintext
  [Your JS Code] → [Node.js APIs (fs, http)] → [C++ (libuv/V8)] → [OS]
  ```  
- **Key Components**:  
  - **V8**: Executes JS (like Chrome’s engine).  
  - **libuv**: Manages async tasks (timers, I/O) using the **Event Loop**.  
  - **Event Loop**:  
    - **Phases**:  
      1. **Timers**: `setTimeout`, `setInterval`.  
      2. **I/O Polling**: Checks for completed file/network tasks.  
      3. **Check**: `setImmediate` callbacks.  
    - **Analogy**: A traffic light system – prioritizes tasks (e.g., timers → I/O → setImmediate).  

#### **Case Study: setTimeout(cb, 0)**  
- **Why 1ms?**:  
  - Node.js enforces a **minimum delay** to avoid blocking the main thread.  
  - **Code Example**:  
    ```javascript
    console.log("Start");
    setTimeout(() => console.log("Timeout"), 0); // Pushed to next event loop tick
    console.log("End");
    // Output: Start → End → Timeout  
    ```  
- **Actionable Tip**: Use `setImmediate()` for tasks that need to run right after I/O operations.  

---

### **2. Chrome V8 Engine**  
#### **What is a JavaScript Engine?**  
- **Analogy**: A translator – converts JS code → machine code (binary) your CPU understands.  
- **Examples**:  
  - **V8**: Used in Chrome, Node.js.  
  - **SpiderMonkey**: Firefox’s engine.  

#### **How V8 Executes JavaScript**  
- **Pipeline**:  
  ```plaintext
  JS Code → Parser (AST) → Ignition (Bytecode) → TurboFan (Optimized Machine Code)
  ```  
  1. **Parser**:  
     - **Tokenization**: Breaks `let x = 1;` into tokens (`let`, `x`, `=`, `1`).  
     - **AST**: Creates a tree structure (like an outline of your code).  
  2. **Ignition (Interpreter)**:  
     - **Bytecode**: Low-level instructions (e.g., `LdaConstant [1]`).  
     - **Quick Execution**: Runs code immediately (no optimizations).  
  3. **TurboFan (Compiler)**:  
     - **Optimization Strategy**: Watches for "hot" functions (called repeatedly).  
     - **Example**:  
       ```javascript
       function add(a, b) { return a + b; }  
       add(1, 2); // TurboFan compiles to machine code: "ADD 1, 2"
       add("a", "b"); // Deoptimizes → back to bytecode  
       ```  

#### **Just-In-Time (JIT) Compilation**  
- **Analogy**: A chef who preps ingredients while cooking:  
  - **Interpreter (Ignition)**: Starts cooking immediately (bytecode).  
  - **Compiler (TurboFan)**: Preps ingredients (optimizations) for frequently ordered dishes (hot functions).  
- **Why JIT?**: Balances speed (no waiting for full compilation) + performance (optimized code).  

#### **Memory Management & Orinoco GC**  
- **Heap Structure**:  
  ```plaintext
  ┌─────────────┐       ┌─────────────┐
  │ Young Gen   │ ←───→ │ Old Gen     │
  │ (Short-lived│       │ (Long-lived │
  │  objects)   │       │  objects)   │
  └─────────────┘       └─────────────┘
  ```  
- **Orinoco GC Workflow**:  
  1. **Scavenge**: Frequent cleanups in Young Gen (fast, minor GC).  
  2. **Mark-Sweep-Compact**: Occasional cleanups in Old Gen (slow, major GC).  
- **Example**:  
  ```javascript
  let data = [];
  function createObjects() { 
    for (let i = 0; i < 10000; i++) {
      data.push({ id: i }); // Fills Young Gen → triggers scavenge
    }
  }
  createObjects(); // Orinoco cleans up unused objects automatically
  ```  
- **Actionable Tip**: Avoid memory leaks by dereferencing objects (e.g., `data = null`).  

---

#### **Cues/Questions (Left Column)**  
**Node.js**:  
1. How does the event loop prioritize `setTimeout` vs. `setImmediate`?  
2. Why does `libuv` use worker threads?  
3. What happens if you block the event loop with synchronous code?  

**V8 Engine**:  
1. How does TurboFan use "type feedback" to optimize code?  
2. Why does V8’s GC separate memory into young