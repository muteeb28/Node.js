## 📝 Notes – Node.js Single Threaded
---

### ▶️ Cues (Questions / Prompts)

1. What does a basic Apache web server do?
2. How is a web server different from an application server?
3. What happens when the server interacts with a database?
4. How did early Apache handle concurrency, and why was it inefficient?
5. How did Apache improve its threading model?
6. What non-blocking, event-driven approach did Nginx introduce?
7. Why can’t we spawn infinite threads or processes?
8. How does Nginx’s master/worker model work?
9. How do workers handle blocking I/O without waiting?
10. What does “HTTP is stateless” imply for request handling?
11. Is Node.js single-threaded or multi-threaded?
12. What’s the difference between high I/O and high processing workloads?
13. How does Node.js leverage libuv and its thread pool?
14. What real-world example illustrates these patterns?

---

### 📝 Notes (Your Bullet-Point Sections)

#### Apache Web Server Basics

* **Cue:** What does a basic Apache web server do?
* Apache is a free, open-source web server that accepts HTTP/HTTPS, processes requests, and returns static content (HTML, CSS, JS).
* Think of it like a receptionist fetching files for visitors.

#### Web Server vs. Application Server

* **Cue:** How is a web server different from an application server?
* Web server → serves static files and handles HTTP protocol.
* Application server → runs dynamic business logic (e.g., user authentication, transactions).

#### Database Interaction

* **Cue:** What happens when the server interacts with a database?
* Server opens a network connection to the DB server.
* DB reads from disk (slow, like retrieving archives).
* Once data is fetched, it’s sent back—if the server blocks here, it wastes CPU cycles.

#### Apache’s Initial Threading Model

* **Cue:** How did early Apache handle concurrency, and why was it inefficient?
* Created **one new process per request** (1 thread = 1 process).
* Process creation = heavy memory overhead + costly context switches.
* While waiting on I/O, CPU sits idle.

#### Apache’s Thread-per-Request Improvement

* **Cue:** How did Apache improve its threading model?
* Switched to **one thread per request** instead of full processes.
* Reduced overhead, but threads still block during I/O.

#### Nginx’s Non-Blocking, Event-Driven Architecture

* **Cue:** What non-blocking, event-driven approach did Nginx introduce?
* Master process + fixed set of single-threaded worker processes.
* Workers **emit events** for I/O tasks instead of waiting.

#### Physical Limits of Processes/Threads

* **Cue:** Why can’t we spawn infinite threads or processes?
* Every thread/process uses RAM (heap, stack) and CPU registers.
* Finite resources (e.g., 3 GB free RAM) → you can’t create unlimited workers.

#### Nginx Master/Worker Model

* **Cue:** How does Nginx’s master/worker model work?
* One master process spawns X workers (one per CPU core).
* Workers sit idle, listening for connections.

#### Worker I/O Handling

* **Cue:** How do workers handle blocking I/O without waiting?
* On I/O (DB/file) they register an event and immediately return to accepting new requests.
* **Analogy:** A waiter sends the order to the kitchen, then continues serving other tables.

#### HTTP Is Stateless

* **Cue:** What does “HTTP is stateless” imply for request handling?
* Each request is independent—no shared session state on the wire.

#### Node.js Threading Behavior

* **Cue:** Is Node.js single-threaded or multi-threaded?
* Uses a **single-threaded event loop** for JS execution.
* Offloads blocking/C P U-heavy tasks to a **thread pool** via **libuv**.

#### High I/O vs. High Processing

* **Cue:** What’s the difference between high I/O and high processing workloads?
* **High I/O**: file reads, network, DB queries (low CPU).
* **High Processing**: compression, ML, crypto (high CPU).
* Event loop excels at I/O; CPU tasks benefit from threads/processes.

#### libuv & Thread Pool

* **Cue:** How does Node.js leverage libuv and its thread pool?
* **libuv** implements the event loop + a configurable thread pool (\~4 threads by default).
* Handles file system, DNS, crypto, compression, etc., off the main loop.

#### Real-World Example: Netflix

* **Cue:** What real-world example illustrates these patterns?
* **Uploading videos** = CPU-heavy (compression, ML) → offloaded from event loop.
* **Fetching watch history** = I/O-heavy (DB read) → handled directly in event loop.

---
## Useful Links

1. [Not everything happens on the thread pool](https://medium.com/softup-technologies/node-js-internals-not-everything-happens-on-the-thread-pool-a14d0a286efb#:~:text=There%20is%20a%20reason%20for,(two%20threads%20per%20core).)  
### 🧠 Summary / Next Steps

* You’ve tracked the evolution from **Apache’s per-request processes** → **Apache’s per-request threads** → **Nginx’s event-driven workers** → **Node.js’s hybrid model** (single-threaded event loop + libuv thread pool).
* **Next:** Dive into **libuv internals** and the **Node.js event loop phases** (timers, pending callbacks, poll, idle, prepare, check, close) to see exactly how non-blocking I/O is orchestrated under the hood.

---