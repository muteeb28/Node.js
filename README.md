# Node.js Fundamentals

## What is Node.js?
Node.js is an open-source, cross-platform runtime environment for JavaScript. Created by Ryan Dahl in 2009, it allows JavaScript to run outside the browser.

### Runtime Environment Explained
- A runtime environment is software that enhances the capabilities of a programming language
- Examples of added capabilities:
  - Timer functions (setTimeout, setInterval)
  - File system operations
  - Operating system access
  - Network communication

### Historical Context
- Before Node.js: JavaScript was primarily confined to browsers
- After Node.js: JavaScript gained ability to:
  - Access system resources directly
  - Read/write files on disk
  - Manage system processes
  - Create server-side applications
  - Perform machine learning tasks

### Cross-Platform Support
- Node.js runs across different operating systems
- Contrasts with non-cross-platform languages like C++
- Similar to Java, Python in platform independence

## Node.js Architecture

### Core Components
1. V8 Engine (JavaScript engine)
2. libuv library
3. Event Loop
4. OS-level access components

### Layer Structure
1. JavaScript Layer
   - Top-level API that developers interact with
   - Implements some functionality directly
   - Can call into the C++ layer

2. C++ Layer
   - Core implementation layer
   - Majority of Node.js runtime
   - Handles system-level operations

### Codebase Organization
- `lib/` folder: Contains JavaScript implementation
- `src/` folder: Contains C++ implementation

### Browser vs Node.js Comparison
Browser Runtime:
- DOM manipulation
- HTML parsing
- Limited networking (fetch)
- Browser-specific APIs

Node.js Runtime:
- File system access
- System process management
- Enhanced networking capabilities
- Server-side development features
```

