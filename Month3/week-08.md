# ★ Profile and Fix // TASK 1
Part 1: Profiling `autocore` 
* **Repo:** [rknastenka/autocore](https://github.com/rknastenka/autocore)
* **Tool used:** py-spy sampling profiler
* **The Process:** I ran a profiling benchmark on `autocore`, wrapping the core `generate()` function in a 300-iteration loop to ensure `py-spy` had enough execution time to capture meaningful samples.
* **The Findings:** The resulting flame graph highlighted `parse_all()` as a major hot path, consuming `~27%` of total CPU samples `(~830ms)`. The visual bottleneck was heavily tied to the `ProcessPoolExecutor` overhead used to spawn parallel workers.
* **Conclusion:** My initial instinct was to try and "fix" this overhead by modifying the parallelization threshold or batching the multiprocessing chunks. However, looking at the numbers objectively; dividing that `830ms` by the 300 loop iterations means the actual multiprocessing overhead is less than `3 milliseconds` per run. And since `autocore` is a CLI tool that runs once per user invocation, a `3ms` process startup cost is entirely negligible, especially since it unlocks parallel parsing that saves seconds on larger filesets.

Part 2: Profiling `marked` 
* **Repo:** [markedjs/marked](https://github.com/markedjs/marked)
* **Tool used:** 0x Node.js profiler
* **The Process:** I ran the `0x` profiler against the built-in `bench.js` script to simulate a heavy markdown parsing workload.
* **The Findings:** The flame graph showed that the program spends a massive chunk of its CPU time inside `tokenization` functions. `inlineTokens` was the widest block, taking `10.7%` of the execution time `(443 samples, or ~443ms)`. `blockTokens` was at `8.3% (345 samples, or ~345ms)`. 
* **Conclusion:** These wide blocks confirm that lexical analysis and regex string matching are the most expensive operations in the library, easily passing the `>50ms` criteria.

Although I identified functions exceeding the `>50ms` threshold, I opted not to open an issue or attempt a PR. A major takeaway from this task is that a wide block on a flame graph simply means the program is spending CPU time there, it is not inherently a bug or something to fix.  
In the case of `marked`, the `tokenization` functions are fundamentally built to run complex regular expressions against massive strings. Because the program is operating exactly as designed, there was no safe fix to implement. The hot paths are just the reality of the workload.

# ★ Find an N+1 // TASK 2
still working on it :p

# ★  Read 3 real flame graphs // TASK 3
1. [Java Flame Graph](https://www.brendangregg.com/blog/images/2014/cpu-vertx-flamegraph.svg) (vert.x serving a JavaScript program)  
   **What's hot/the bottleneck:** Since we know that wider blocks mean more CPU time, I noticed a massive tower on the left side of the graph. That wide block is the `Mozilla Rhino JavaScript` engine, which is eating up `42%` of the total CPU time (which is the bottleneck).
   There's also another wide area for a function called `write0()` taking about `32%`, but that's actually a good thing, it's just the server doing its normal job of responding to user requests :) .
2. [Hot/Cold Flame Graph](https://www.brendangregg.com/FlameGraphs/hotcoldthread-kernel.svg) (Experimental On-CPU + Off-CPU)  
   **What's hot/the bottleneck:** The blue off-CPU blocks end up being overwhelmingly wide. The bottleneck here usually isn't the CPU grinding through intense calculations; instead, it's the application spending massive amounts of time blocked by system calls, waiting on disk I/O, or stuck on network requests.
   I found this graph a kind of a visual mess. It makes total sense conceptually, a lot of performance bugs happen because a program is sitting around waiting for something else to finish rather than actually working. However, because off-CPU time (like a thread sleeping for 30 seconds) is so massive compared to code execution time (which is usually milliseconds), the "cold" blue blocks completely dominate the screen. It squishes the "hot" on-CPU red blocks into tiny, unreadable slivers.

3. [MySQL Allocator Tracing](https://www.brendangregg.com/FlameGraphs/malloccalls_mysqld1.svg) (malloc() Calls)  
   **What's hot/the bottleneck:** In this memory flame graph, the x-axis represents the total amount of memory allocated (in bytes or number of calls). The bottleneck (or the biggest memory hog) is found by looking for the widest blocks. The massive plateau at the top indicates the specific database functions responsible for allocating the vast majority of the memory.
   The bottlenecks are the widest blocks at the top of the stacks. I noticed two major culprits. First, the sysbench load testing tool itself has a massive block, meaning the tester is eating up a ton of memory directly. Second, looking at the mysqld process, the widest blocks are sitting right on top of table locking (mysql_lock_tables). Basically, the database is triggering the most memory allocations just trying to lock tables and figure out how to execute the queries.
   

# ★ Use a Real Debugger // TASK 4
* **Project:** Banking System (Spring Boot REST API)
* **IDE / Debugger:** Eclipse / Spring Tools for Eclipse (Java Debugger)
- The issue:  
  A `POST` endpoint for transaction payloads returned HTTP 200 OK, but downstream logic kept failing because object fields remained null after request deserialization.
- The debugging process:  
  1- Setting Breakpoints: I placed a line breakpoint at the REST controller endpoint (@PostMapping entry point).  
  2- Inspecting State: Triggering the API in Debug mode paused execution at the controller. Using the `Variables View`, I expanded the DTO payload and confirmed its fields state.  
  3- Line-by-Line Tracing: Using Step Over `(F6)` and Step Into `(F5)`, I verified that the payload entered the controller already unpopulated, ruling out downstream service logic bugs.  
  4- Root cause: `Jackson` silent deserialization failure due to a field naming mismatch between incoming JSON and DTO fields.  
  5- Fixed the failure and Re-ran the request in Debug mode and verified in the `Variables View` that fields were now populated, allowing downstream processing to succeed.

- Reflection:  
Using a real debugger made isolating the issue much faster than adding temporary log lines would have. Inspecting live memory states and stepping through code line-by-line provided immediate clarity on exactly where data was dropping out.
  
