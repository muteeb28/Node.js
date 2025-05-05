**Topic: Web Servers, Node.js Threading & LibUV**  

---

### **Cue/Self-Test Column (Key Points/Questions)**  
1. **Apache Web Server’s Approach**  
   - Why was creating a process/thread per request inefficient?  
2. **Nginx’s Non-Blocking Architecture**  
   - How does Nginx handle blocking I/O operations?  
3. **High I/O vs. High Processing Tasks**  
   - What’s the difference, and why does Node.js excel at I/O?  
4. **Node.js Threading Model**  
   - Is Node.js single-threaded or multi-threaded?  
5. **LibUV & Thread Pool**  
   - How does LibUV manage I/O and CPU-heavy tasks?  

---

### **Note-Taking Area (Detailed Content)**  

#### **1. Apache Web Server’s Approach**  
- **Original Design**:  
  - Created a **new process** (later thread) for **each request**.  
  - **Problem**:  
    - High memory/RAM usage (each process needs its own resources).  
    - Blocking I/O (e.g., DB calls) left processes idle, wasting CPU cycles.  
- **Example**:  
  - 100 concurrent requests → 100 processes/threads waiting for DB responses.  

---

#### **2. Nginx’s Non-Blocking Architecture**  
- **Design**:  
  - **Master Process + Worker Processes**:  
    - 1 worker per CPU core (e.g., 8 cores → 8 workers).  
    - Workers use **event-driven** logic: **don’t block** on I/O.  
- **Workflow**:  
  - Worker accepts a request → emits an **event** for I/O (e.g., DB call).  
  - While waiting for I/O, the worker handles new requests.  
- **Key Benefit**:  
  - No idle threads/processes. Handles thousands of concurrent requests with minimal resources.  

---

#### **3. High I/O vs. High Processing Tasks**  
- **I/O-Bound Tasks**:  
  - **Examples**: Reading files, network requests (DB calls, APIs).  
  - **Node.js Strength**: Event-driven model handles these **without blocking**.  
- **CPU-Bound Tasks**:  
  - **Examples**: Video encoding, ML training, encryption.  
  - **Challenge**: Requires heavy CPU usage → needs multi-threading/processing.  

---

#### **4. Node.js Threading Model**  
- **Myth**: Node.js is **not purely single-threaded or multi-threaded**.  
- **Reality**:  
  - **Event Loop**: Single-threaded for JavaScript code and event orchestration.  
  - **LibUV Thread Pool**: Handles blocking I/O and CPU tasks via **4 threads by default** (configurable).  
- **Example**:  
  - Reading a file (I/O) → delegated to LibUV thread pool.  
  - Crypto task (CPU) → uses worker threads.  

---

#### **5. LibUV & Thread Pool**  
- **LibUV**: C library powering Node.js’s async I/O.  
  - **Event Loop**: Manages non-blocking operations (timers, network calls).  
  - **Thread Pool**: Handles file I/O, DNS, crypto (CPU tasks).  
- **Thread Pool Analogy**:  
  - Like an army ready for deployment (threads pre-created, not spawned per request).  
- **Key Insight**:  
  - **Default threads**: 4 (adjustable via `UV_THREADPOOL_SIZE`).  
  - **Example**: If 10 CPU tasks arrive, 4 run in parallel; others wait.  

---

### **Summary**  
1. **Apache**: Created processes/threads per request → inefficient for blocking I/O.  
2. **Nginx**: Uses event-driven workers to avoid blocking → scalable for high I/O.  
3. **Node.js**: Balances single-threaded event loop (JavaScript) with LibUV thread pool (I/O/CPU tasks).  
4. **High I/O**: Non-blocking (e.g., DB calls). **High CPU**: Needs threads (LibUV/worker_threads).  
5. **LibUV**: Manages async operations via event loop and thread pool.  

---

### **Self-Test Questions**  
1. Why did Apache’s process-per-request model fail for high traffic?  
2. How does Nginx avoid blocking during I/O operations?  
3. What is the difference between I/O-bound and CPU-bound tasks?  
4. Explain Node.js’s threading model using LibUV.  
5. How does LibUV’s thread pool handle 10 concurrent crypto tasks?  

---

### **Next Steps**  
1. **Test Yourself**: Use the Cue Column questions to recall details.  
2. **Explore**: Adjust `UV_THREADPOOL_SIZE` in Node.js for CPU tasks.  
3. **Deep Dive**: Study LibUV’s event loop phases (timers, I/O callbacks, etc.).  

---
Raw file - Topic: Web Servers, Node.js Threading & LibUV

what is the threading behavior of node
case study: apache web server
let say you want to setup a webserver there are lot of technology using wchich you will be set up a webserver apache free open source webserver you can accept request http and https based on that it will be able to process your request and send the corresponding request back 
apache server is going to help you step up a web serever it will be going to setup a website for you
again webserver is gong to hanle http request and serve static html css js mostly static content for you 
going to the bank how you reqeust something to bank is different then asking in embassy similarly on internt also to get a specfic type of data you have to raise request in the specfic format , e,g website related data http is something related to that
how web server are difeernt then application server 
application server is a process that can hanle more of dynamic logic 
 in most of the client server archeiteure
 there is client make a request to a corresponding  server and most of the sercver app try to do some common thisng e.g the server is enteracting with a db collect some data then do their internal processing
 whaT DOEs sever actually do when it try to interact with db
 db as mentioned are technically working on a server , so db server is process which is capable of reading some data from data storage and generally this data is stored on some kind of a hard disk so when you interact with db this db is technically a server which is reading some data from harddisk
 whenevr you server tries to make a request to db you have to make a network connection betweeen  your server and database, so what will happen when you try interact  with db ...your backend server is going to make network interaction with the database, database server is going to accept the request then it will say i have to read some data from hardisk once it has read the data from the harddisk then it will send back to the coresponding server 
 now you will see its going to take a lot of time to travel back altoggether
 now lets say 5 clients are making request should it block itself, shoould i server be made in such a way that it should make first request it should go to db come back server the request of first client and till that time the other client should be waiting ,no ,now because we are blocked here the server is blocked from the database then if the server sit idle it will waste probably billions of cpu cycle 
 then this was the problem then apache came , in early apache server implementation for every new request it used to create a new process (1 thread per process) to handle the request
 it was better for blocking the server but we have talked earlier it is a very complex very expensive operation all together and apart from that the more process you are having the more memory consumption are there, it is diffcult to do context switichig later apache improved this 
 the use tthe similar threading the improved it by introducing a s single thread per request instead of a whole process
 this diffintily better now you can hanle concourrent connection significtly higher
 there are alternative technology the nginx worked on non blocking event driven architecture 
 very close to what nodejs does, nginx did not go with the approach of multi threading 
what is this non blocking architecture and why do we need it 
lets think about an idea even if you are making a multi threaded webserver  threads are also some kind of a process the problem is with respest to machien
there is a physical limitation to the number of process at any point of time at
peak point that you will be able to create like at peak level , there will be limited amount of process that you will be able to trigger on your single core 
that is a very important question that you cannot go beyond a physical limit on a number let say you have 4gb ram assume that 1gb goes in running os you have 3gb left so in 3gb you cant make infinite number of process that is nginx and nodejs asked you cannot just keep on making processes ofcousse threads are also just what process things can do multi threads there is nothng tht can happen in infinite way in computer 
so they took non blocking event driven approach what this approach was
kind of like master process along with master process there were a few limited worker processes this is a small group ofcourse anything that has to run in your machine that is going to create some kind of process but what we do is how about we try to design architecture so we dont create new thread /process for every request what if we have limited resource like this and with limited resource like this you try to architecture your technology in a way such that it is able to handle concurrent request this kind of idea nginx follow 
nginx said that lets say we have master process and a small group of worker process and together they are going to handle all of the request now these worker processes might be having some specfic task and if you have computing resources available then maybe you can run these worker processes on different 
core also but you dont create new process for every single request
nginx ensured if you have x number of cores then it will create x number of worker proceeeses to maximize performance and each worker process is a single threaded process and it doesnt create more threads altogether if you have 8 cores there are 8 worker processes going into the picture what happens is whenever a client makes a request these worker processes sit back and relax and wait for new client connections come into the picture , whenever a new client connection comes up then one worker process creates the connection with that client and what it does every worker process emits a new event (is an action or somtheng happens) so let say client is requesting something the moment worker processes got this new process its going to emit a new event maybe this request need some db interaction now the moment it need some db interaction it will become blocking request so what these worker process do incase of these kind of blocking request that they do not wait for this blocking request response to come up and start listening to new request also that is what these worker process do they dont block themselves or they dont wait for these blocking calls , they have architet in such a way that dont block themselves a very similar thing your browser does 
what if there are two requests req 1 and req 2 , req2 needs some data from req 1 how will that happen , http request are state less protocol/ request that means one http request is at any point of time never know about any other http request they are independent 
worker process emits the events who conusme these events and how these events are consumed can be dependent on technology to technology what we are going to learn how node js is going to consume this these events 
nodejs is neither single threaded or mutli thread what it is we will talk about it but before we go to how exactly things are done with node js 
i will talk about high io vs processing 
nodejs became famous xuz it was extremely good with high io but it was not that good for high processing 
IO stands for input output, which is how a computer communicates with the outside world  , it can be anything you read some file from the disk that is an io operation , io means data exchange e.g reading files, making network request, making db request are all io request
generally these are not cpu intensive on the other hand high processing which need high computational power or high cpu powe
e.g data compression algorithm, video file processing, crypto graphic algorithm, high processing task
application has some high io and some hi processing
if you need high io like shown above client request to server and server read from db or whenever you have to do high io you dont need computational power
then dont think more number of processes to do computation is not going to help you much and this is the same thing that tech like nginx and nodejs lached on 
that if you have to do high io then we dont consume this high number of processes we instead keep memory free
processing example is like training an ML model then you need a high quality gpu (high density cputing engine)
what nodejs did what they said some request can be high IO and some can be high procesing, you are using nodejs to create some kind of server if you are creating then it is high io application but lets say you are using it for implemting some crypto algorithm that is a high processing task 
so what nodejs said if you want to do task that are high IO bound then i will use this kind of an event driven architecture and i will not create a lot of processes i will not do multithreading but if you are doing high processing then you need process xuz every process is working there it is not waiting it actually computing whenever it get a next cpu cycle it will do some computation it will not wait if it is not going to wait then multithreading is going to use 
so some operation in nodejs happen through mutithreading some happens with a single thread is node js single threaded?no is it multi threaded? no
so operations in nodejs works on multithreads anf some on single threads
real word situation is netflix, if it is let say some whenver upload a video on netflix it gets processed like some machine learning multiple resolution thumbnails etc some skip intro algo maybe when this video processing is going to happen that is high processing but lets say you have to fetch the watch history of a user that is IO operation or let say you wnt to fetch video file and serve it to user. 
summary of everything uptill now 
what was the problem with apache, intially it use to create new new processes for every request ...then apache did a better approch by creating threads it is not best option also if a request is waiting for a db call it then that process is waiting idle e.g you have 100 processe and 100 concurrent request was there 
100 to 100 are waiting for a db call request and 100 of them are idle so you have process in your ram which are doing nothing what nginx does is it says that we will have x number of process with x number of cores that means we will process x things parallel we ill not create more processes whenver a request comes it is going to taken up by worker processes if that request can be immediately handle given give it back if not if it need some blocking let say db answer or read some file then  that worker is not going to block itself it is going to allocate this task in the form of an event for some other process to handle how it handles can be varying but this worker processes is not going to wait for it 
how node js achieves it,how at run time nodes understands which things to do in multithreading which not so technicaly there is library called libuv and it is responsible for all of this event driven modeling for nodejs 
before we go into internals of libuv
we will first see this article https://medium.com/softup-technologies/node-js-internals-not-everything-happens-on-the-thread-pool-a14d0a286efb#:~:text=There%20is%20a%20reason%20for,(two%20threads%20per%20core).
where this is explained in example
let me disucss thread pool in brief is like how army works when war comes it is not at that time army hires the army keeps army if the war comes the force is ready to be deployed thing about it like this that you have an army of threads it is ready to be deployed you dont have to create at that point 
hoW libuv will allocate this to os and nodejs will allocate we will talk in next topic 