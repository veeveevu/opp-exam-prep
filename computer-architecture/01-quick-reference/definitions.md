# 📖 Key Definitions — Organized by Topic

> Know these cold. Written exams often ask you to "define" or "explain" terms.

---

## ⚡ Performance

| Term | Definition |
|------|-----------|
| **CPI** | Cycles Per Instruction — average number of clock cycles each instruction takes |
| **IPC** | Instructions Per Cycle — reciprocal of CPI; higher = better |
| **Clock Cycle Time** | Duration of one clock cycle = 1 / clock_frequency |
| **Clock Rate** | Clock frequency in Hz (e.g., 3 GHz = 3×10⁹ cycles/second) |
| **Instruction Count (IC)** | Total number of instructions executed by a program |
| **CPU Time** | Time the CPU spends executing a program (IC × CPI × CCT) |
| **MIPS** | Millions of Instructions Per Second — often misleading metric |
| **Benchmark** | Standard program used to measure and compare processor performance |
| **Speedup** | Ratio of original time to improved time (>1 means improvement) |
| **Amdahl's Law** | Formula showing the maximum speedup from parallelizing a fraction P of a workload |
| **Iron Law** | CPU Time = IC × CPI × Clock Cycle Time — the fundamental performance equation |
| **Throughput** | Number of tasks completed per unit time |
| **Latency** | Time to complete a single task from start to finish |

---

## 🗃️ Cache

| Term | Definition |
|------|-----------|
| **Cache** | Small, fast memory between CPU and main memory |
| **Cache Hit** | Requested data found in cache |
| **Cache Miss** | Requested data not in cache; must fetch from slower memory |
| **Hit Time** | Time to access data when it IS in cache |
| **Miss Penalty** | Additional time to fetch data from next memory level on a miss |
| **Miss Rate** | Fraction of accesses that miss in cache |
| **Hit Rate** | 1 − Miss Rate |
| **AMAT** | Average Memory Access Time = Hit Time + Miss Rate × Miss Penalty |
| **Block/Cache Line** | Unit of transfer between cache levels (e.g., 64 bytes) |
| **Tag** | Portion of address used to identify which memory block is in a cache set |
| **Index** | Portion of address used to select a cache set |
| **Offset** | Portion of address specifying the byte within a block |
| **Direct Mapped** | Each memory block maps to exactly one cache location |
| **Set Associative** | Cache divided into sets; each block maps to one set, any way within it |
| **Fully Associative** | Any block can go to any cache location |
| **Associativity** | Number of cache ways in a set (n-way set associative) |
| **LRU** | Least Recently Used — evict the block unused for the longest time |
| **FIFO** | First In First Out — evict the oldest loaded block |
| **Write-Through** | Writes update both cache and main memory simultaneously |
| **Write-Back** | Writes update cache only; memory updated when block is evicted |
| **Write-Allocate** | On a write miss, load block into cache first, then write |
| **No-Write-Allocate** | On a write miss, write directly to memory without loading into cache |
| **Dirty Bit** | Flag indicating a write-back cache line has been modified and differs from memory |
| **Valid Bit** | Flag indicating whether a cache line holds valid data |
| **Cold Miss** | First access to a block (compulsory miss) — unavoidable |
| **Conflict Miss** | Miss due to too many blocks mapping to the same cache set |
| **Capacity Miss** | Cache is too small to hold all needed data |
| **Cache Coherence** | Problem of keeping multiple caches consistent in multiprocessor systems |

---

## 💾 Virtual Memory

| Term | Definition |
|------|-----------|
| **Virtual Address (VA)** | Address used by program code (before translation) |
| **Physical Address (PA)** | Actual hardware memory address (after translation) |
| **Page** | Fixed-size block of virtual memory |
| **Frame (Page Frame)** | Fixed-size block of physical memory (same size as a page) |
| **Page Table** | Data structure mapping virtual page numbers to physical frame numbers |
| **Page Table Entry (PTE)** | One entry in the page table; contains PFN + status bits |
| **VPN** | Virtual Page Number — upper bits of virtual address |
| **PFN** | Physical Frame Number — upper bits of physical address |
| **Page Offset** | Lower bits of address; same in VA and PA |
| **TLB** | Translation Lookaside Buffer — hardware cache for VA→PA mappings |
| **TLB Hit** | VA→PA mapping found in TLB |
| **TLB Miss** | Mapping not in TLB; must walk page table |
| **Page Fault** | Requested page not in physical memory; OS must load it from disk |
| **Demand Paging** | Pages loaded only when accessed (not all at program start) |
| **Working Set** | Set of pages a process actively uses |
| **Thrashing** | System spends more time paging than executing (working set doesn't fit in memory) |
| **Protection Bits** | Bits in PTE controlling read/write/execute permissions |
| **Multi-level Page Table** | Page table split into hierarchy to save space |

---

## ⚙️ Pipelining

| Term | Definition |
|------|-----------|
| **Pipeline** | Technique to overlap execution of multiple instructions |
| **Stage** | One step in the pipeline (IF, ID, EX, MEM, WB) |
| **IF** | Instruction Fetch — read instruction from memory/cache |
| **ID** | Instruction Decode — decode instruction, read registers |
| **EX** | Execute — ALU operation or address calculation |
| **MEM** | Memory Access — read/write data memory |
| **WB** | Write Back — write result to register file |
| **Hazard** | Condition that prevents the next instruction from executing in its expected cycle |
| **Data Hazard** | Hazard due to data dependency between instructions |
| **RAW** | Read After Write — instruction reads a register before a previous instruction writes it |
| **WAR** | Write After Read — instruction writes a register that a later instruction reads (rare in-order) |
| **WAW** | Write After Write — two instructions write same register; second must finish after first |
| **Control Hazard** | Hazard due to branches/jumps changing the PC |
| **Structural Hazard** | Two instructions need the same hardware resource at the same time |
| **Stall/Bubble** | Inserting a NOP cycle to resolve a hazard |
| **Forwarding/Bypassing** | Routing a computed value directly to where it's needed, before WB |
| **Branch Delay Slot** | Instruction after a branch that always executes (used in MIPS) |
| **Speculative Execution** | Execute instructions after a branch before knowing outcome |

---

## 🔮 Branch Prediction

| Term | Definition |
|------|-----------|
| **Branch Prediction** | Hardware guess of branch outcome before it is resolved |
| **Static Prediction** | Fixed prediction (always taken, always not-taken, BTFN) |
| **Dynamic Prediction** | Prediction based on past branch behavior at runtime |
| **Branch History Table (BHT)** | Table of prediction bits indexed by branch PC |
| **1-bit Predictor** | Single bit: predict taken if last time was taken |
| **2-bit Predictor** | 4-state saturating counter: more stable predictions |
| **Branch Target Buffer (BTB)** | Cache storing predicted target address for branches |
| **Misprediction Penalty** | Cycles wasted when branch prediction is wrong |

---

## 🖥️ Datapath & Control

| Term | Definition |
|------|-----------|
| **Datapath** | Hardware components that perform data operations (ALU, registers, buses) |
| **Control Unit** | Generates control signals that direct datapath operations |
| **ALU** | Arithmetic Logic Unit — performs arithmetic and logical operations |
| **Register File** | Set of CPU registers accessible by instructions |
| **PC** | Program Counter — register holding address of next instruction |
| **Multiplexer (MUX)** | Selects one of multiple inputs based on control signal |
| **Single-cycle** | Each instruction completes in exactly one clock cycle |
| **Multi-cycle** | Instructions take variable number of cycles; reuse hardware |
| **Microprogramming** | Control unit implemented as a small program in ROM |
| **RISC** | Reduced Instruction Set Computer — few simple instructions, all same size |
| **CISC** | Complex Instruction Set Computer — many complex instructions, variable size |

---

## 🔌 Interrupts

| Term | Definition |
|------|-----------|
| **Interrupt** | Signal to CPU requesting immediate attention |
| **Exception** | Interrupt caused by the executing instruction itself (e.g., divide by zero) |
| **Interrupt Handler (ISR)** | OS routine that services an interrupt |
| **Vectored Interrupt** | Each interrupt type has its own handler address |
| **Polling** | CPU repeatedly checks device status (software-driven) |
| **Interrupt-Driven I/O** | Device interrupts CPU when ready (hardware-driven) |
| **DMA** | Direct Memory Access — device transfers data to/from memory without CPU involvement |
| **Trap** | Software-generated interrupt (system call) |

---

## 🔢 Multiprocessors & Parallel

| Term | Definition |
|------|-----------|
| **SMP** | Symmetric MultiProcessor — all CPUs share one memory, equal access |
| **NUMA** | Non-Uniform Memory Access — some memory closer to some CPUs |
| **Cache Coherence** | All caches see the same value for a memory location |
| **MESI Protocol** | Modified, Exclusive, Shared, Invalid — cache coherence states |
| **False Sharing** | Two CPUs access different data in the same cache block, causing unnecessary invalidation |
| **Superscalar** | CPU that issues multiple instructions per clock cycle |
| **Out-of-Order Execution** | CPU executes instructions in different order than program to avoid stalls |
| **Tomasulo's Algorithm** | Dynamic scheduling algorithm using reservation stations and register renaming |
