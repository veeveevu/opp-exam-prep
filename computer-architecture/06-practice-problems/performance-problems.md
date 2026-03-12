# ⚡ Performance Practice Problems

> These problems mix all performance topics. Always write the formula first!

---

## Problem 1 — Iron Law Basics

A program executes 5×10⁸ instructions on a 2GHz processor with CPI = 2.5.

**a)** Calculate CPU time.

**b)** A new compiler reduces instruction count by 20% but increases CPI to 3. Is the new compiler faster?

**c)** A hardware improvement reduces CPI to 2.0 (same IC, same clock). What is the speedup?

---

## Problem 2 — CPI Calculation from Instruction Mix

A processor executes a workload with the following characteristics:

| Instruction Type | Frequency | Cycles |
|-----------------|-----------|--------|
| Integer ALU | 43% | 1 |
| Floating Point | 7% | 10 |
| Load | 21% | 3 |
| Store | 12% | 3 |
| Branch | 17% | 2 |

**a)** Calculate the effective CPI.

**b)** If you could speed up floating-point operations to 2 cycles instead of 10, what is the new CPI?

**c)** If you could speed up ALU instructions to 0.5 cycles (e.g., using a faster ALU), what is the new CPI?

**d)** Which improvement (b or c) is better? Why?

---

## Problem 3 — Amdahl's Law

**a)** A program spends 60% of its time in a sorting routine. You optimize the sort to run 4× faster. What is the overall speedup?

**b)** Using the same scenario, what speedup of the sort would give an overall 2× speedup?

**c)** What is the maximum possible speedup, no matter how fast you make the sort?

---

## Problem 4 — Amdahl's Law for Parallelism

A program is analyzed and found to be:
- 30% sequential (cannot be parallelized)
- 70% parallelizable

**a)** With 2 processors, what is the speedup?

**b)** With 4 processors?

**c)** With 8 processors?

**d)** With infinite processors?

**e)** Plot these values and describe what happens as you add more processors.

---

## Problem 5 — CPU Time with Memory Stalls

A 500MHz processor has:
- Base CPI (no memory stalls) = 1.5
- 30% of instructions access memory
- Cache miss rate: 8%
- Miss penalty: 80 cycles

**a)** Calculate memory stall cycles per instruction.

**b)** Calculate actual CPI (including memory stalls).

**c)** Calculate CPU time for a program with 2×10⁸ instructions.

**d)** If the cache miss rate is reduced to 2%, what is the new execution time?

---

## Problem 6 — Comparing Two Processors

| | Processor A | Processor B |
|--|------------|------------|
| Clock rate | 3 GHz | 4 GHz |
| CPI | 1.5 | 2.5 |

Same program, same instruction count.

**a)** Which processor executes the program faster? By what percentage?

**b)** MIPS for A and B?

**c)** Is MIPS a reliable performance metric here? Why or why not?

---

## Problem 7 — Performance Improvement Analysis

You are given a budget to improve a processor. You can choose ONE of:

**Option A:** Speed up floating-point unit by 5×. Floating-point takes 20% of execution time.

**Option B:** Speed up memory system so cache misses are 2× faster. Memory stalls account for 40% of execution time.

**Option C:** Speed up integer ALU by 2×. Integer ALU takes 35% of execution time.

**a)** Using Amdahl's Law, calculate the overall speedup for each option.

**b)** Which option gives the best overall speedup?

**c)** What if you could combine Options A and B together? Calculate combined speedup.

---

## Problem 8 — End-to-End Benchmark Analysis

System specifications:
- Clock: 2.5 GHz
- Average CPI: 2
- 15% branch instructions; 5% misprediction rate; 10-cycle penalty per misprediction
- 20% load instructions; 5% load-use hazard rate; 1-cycle penalty per hazard

**a)** Calculate the penalty CPI contribution from branches.

**b)** Calculate the penalty CPI contribution from load-use hazards.

**c)** What is the total actual CPI?

**d)** How many instructions does a program execute in 1 second?

---

*See [SOLUTIONS.md](SOLUTIONS.md) for complete solutions.*
