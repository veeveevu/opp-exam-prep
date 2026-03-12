# ⚡ Performance — Complete Study Guide

> 🔴 **CRITICAL TOPIC** — Amdahl's Law, CPI calculations, and speedup problems are very likely tested.

---

## 1. Core Concepts

- **The Iron Law of Performance:** `CPU Time = IC × CPI × Clock Cycle Time` — three separate, independent variables.
- To improve performance, reduce at least one of IC, CPI, or clock cycle time (they can trade off against each other).
- **Speedup** measures relative improvement: `Speedup = Old Time / New Time`.
- **Amdahl's Law** shows that the non-optimized fraction limits total speedup — even infinite improvement on part of a program has a ceiling.
- CPI depends on both instruction types (architecture) and implementation (microarchitecture).
- **Benchmark** programs measure performance; different benchmarks favor different architectures.
- Throughput and latency are different metrics: pipelines improve throughput, not latency.

---

## 2. Key Formulas & Calculations

### The Iron Law
```
CPU Time = IC × CPI × Clock Cycle Time
CPU Time = IC × CPI / Clock Rate (frequency)
```

### CPI Breakdown (Weighted Average)
```
CPI_effective = Σ (CPI_i × Frequency_i)

where Frequency_i = count_of_instruction_type_i / total_instruction_count

Example:
  40% of instructions are ALU (CPI=1)
  30% are memory (CPI=2)
  30% are branches (CPI=3)
  CPI = 0.4×1 + 0.3×2 + 0.3×3 = 0.4 + 0.6 + 0.9 = 1.9
```

### Speedup
```
Speedup = Time_old / Time_new

Speedup > 1  means improvement
Speedup = 2  means twice as fast (takes half the time)
```

### Amdahl's Law
```
Speedup_overall = 1 / ((1 − P) + P/S)

where:
  P = fraction of execution time that is improved (0 ≤ P ≤ 1)
  S = speedup factor for the improved portion

Maximum speedup (as S → ∞):
  Speedup_max = 1 / (1 − P)
```

### MIPS (Millions of Instructions Per Second)
```
MIPS = Clock Rate / (CPI × 10^6)
     = IC / (CPU_Time × 10^6)
```

### Execution Time Relationship
```
Performance = 1 / Execution_Time
Performance_A / Performance_B = Time_B / Time_A = Speedup (A over B)
```

---

## 3. Visual Diagrams

### The Iron Law — Trade-offs
```
         CPU Time = IC × CPI × Clock Cycle Time
         
         Affected by:       Affected by:        Affected by:
         • ISA design       • Microarchitecture  • Technology
         • Compiler         • Pipelining         • Clock speed
         • Algorithm        • Cache hierarchy
                            • Branch prediction
```

### Amdahl's Law — Speedup Wall
```
Speedup
   │
 10│          ────────────────── Maximum = 1/(1-P) = 10 (if P=0.9)
   │         /
  8│        /
   │       /
  6│      /
   │     /
  4│    /
   │   /  ← Actual speedup curve
  2│  /
   │ /
  1│/
   └────────────────────────────→ Speedup of improved portion (S)
   1   2   4   8  16  32  64  ∞
```

### CPI Components
```
CPI_actual = Base CPI + Memory Stall CPI + Branch Penalty CPI

            ┌────────────────────────────────────────────────┐
            │ Base CPI (ideal execution)                     │
            ├────────────────────────────────────────────────┤
            │ + Memory stall cycles/instruction              │
            │   = accesses/instr × miss_rate × miss_penalty  │
            ├────────────────────────────────────────────────┤
            │ + Branch penalty cycles/instruction            │
            │   = branch_freq × penalty × mispred_rate       │
            └────────────────────────────────────────────────┘
```

---

## 4. Worked Examples

### Example 1: CPI Calculation with Instruction Mix
**Problem:** A processor has 3 instruction types. What is the effective CPI?

| Type | Frequency | CPI |
|------|-----------|-----|
| ALU | 50% | 1 |
| Load/Store | 30% | 3 |
| Branch | 20% | 2 |

```
CPI = 0.50 × 1 + 0.30 × 3 + 0.20 × 2
    = 0.50 + 0.90 + 0.40
    = 1.80 cycles/instruction
```

### Example 2: Applying the Iron Law
**Problem:** Machine A: 2GHz clock, CPI=2. Machine B: 3GHz clock, CPI=3. Same program with 10⁸ instructions. Which is faster?

```
Time_A = 10^8 × 2 / (2×10^9) = 2×10^8 / 2×10^9 = 0.1 seconds

Time_B = 10^8 × 3 / (3×10^9) = 3×10^8 / 3×10^9 = 0.1 seconds

Speedup = 0.1/0.1 = 1.0 — they're equally fast!
```

**Lesson:** Higher clock rate doesn't mean faster execution if CPI increases proportionally.

### Example 3: Amdahl's Law
**Problem:** A program spends 70% of time in a loop. You optimize the loop to run 5× faster. What is the overall speedup?

```
P = 0.70  (fraction improved)
S = 5     (speedup of that fraction)

Speedup = 1 / ((1 − 0.70) + 0.70/5)
        = 1 / (0.30 + 0.14)
        = 1 / 0.44
        ≈ 2.27×
```

Even with 5× improvement on 70% of the code, overall speedup is only 2.27×. The remaining 30% is now the bottleneck.

**Maximum possible speedup (S→∞):**
```
Speedup_max = 1 / (1 − 0.70) = 1 / 0.30 ≈ 3.33×
```

### Example 4: CPI with Memory Stalls
**Problem:**
- Base CPI = 1 (ideal)
- 25% of instructions are loads; 10% miss rate; 100-cycle miss penalty
- 15% of instructions are stores (no stalls assumed write-through)
- Clock rate = 2 GHz

```
Memory stall CPI = 0.25 × 0.10 × 100 = 2.5 cycles

CPI_actual = 1 + 2.5 = 3.5

CPU Time = IC × 3.5 / (2×10^9) = IC × 1.75 ns
```

### Example 5: Comparing Two Compilers
**Problem:** Compiler A generates 10⁹ instructions with CPI=1.5. Compiler B generates 8×10⁸ instructions with CPI=2. 3GHz clock. Which is faster?

```
Time_A = 10^9 × 1.5 / (3×10^9) = 1.5/3 = 0.5 seconds

Time_B = 8×10^8 × 2 / (3×10^9) = 1.6×10^9 / 3×10^9 = 0.533 seconds

Speedup = 0.533 / 0.5 = 1.067

Compiler A is 6.7% faster.
```

---

## 5. Common Exam Questions

### "A system has the following characteristics. Calculate CPU time."
**Steps:**
1. Write: `CPU Time = IC × CPI × (1 / Clock_Rate)`
2. Compute CPI if needed: `CPI = Σ(type_i_fraction × type_i_CPI)`
3. Substitute and compute
4. State units (seconds, nanoseconds)

### "Using Amdahl's Law, calculate the speedup..."
**Steps:**
1. Write: `Speedup = 1 / ((1−P) + P/S)`
2. Identify P (fraction being improved) and S (how much it's being improved)
3. Substitute and compute
4. Comment on the ceiling: `Speedup_max = 1/(1−P)`

### "Which design is better? Justify your answer."
**Approach:**
- Calculate CPU time for each: `Time = IC × CPI / ClockRate`
- Compare directly
- Explain the trade-off (e.g., "Design B has a higher CPI but lower IC due to more powerful instructions")

### "What are the limitations of using MIPS as a performance metric?"
**Answer:**
- MIPS counts instructions, not the work done per instruction
- A RISC processor doing more instructions to accomplish the same task will show higher MIPS than a CISC processor, even if CISC is faster
- MIPS ignores instruction mix differences between programs
- Valid only for comparing the same program on the same ISA

---

## 6. Tricky Points

### The three Iron Law variables are independent
- Faster clock does NOT automatically lower CPI
- Better architecture (lower CPI) does NOT mean fewer instructions
- More instructions ≠ worse performance if each does more work (CISC)

### Amdahl's Law applies to ANY optimization
Not just parallelization. If 40% of execution time is floating point and you double FP speed, speedup is `1 / (0.6 + 0.4/2) = 1 / 0.8 = 1.25×`.

### Be careful about "fraction of time" vs "fraction of instructions"
Amdahl's Law uses **fraction of execution time**, not fraction of instruction count. A loop running 50% of instructions might run 80% of the time.

### MIPS can increase when performance decreases
If a compiler uses simpler (faster) instructions with less work done, MIPS goes up, but execution time goes up too. MIPS is not a reliable benchmark.

### Speedup formula variations
```
Speedup based on time: Old_Time / New_Time

Speedup based on components: 
New_Time = Old_Time_of_unchanged_parts + (Old_Time_of_changed_parts / Speedup_of_changed)
```

---

## 7. Quick Self-Test

**Q1:** A processor runs at 2GHz with CPI=2. A program executes 2×10⁸ instructions. What is the CPU time?

**Q2:** Using Amdahl's Law: if 80% of a program can be parallelized, what is the maximum possible speedup?

**Q3:** Processor A: 1GHz, CPI=1. Processor B: 2GHz, CPI=3. Both run the same program. Which is faster?

**Q4:** An instruction mix is: 40% ALU (1 cycle), 35% memory (4 cycles), 25% branch (2 cycles). What is the CPI?

**Q5:** Clock rate doubles. CPI stays the same. IC stays the same. How does CPU time change?

---

### Answers to Self-Test

**A1:** `CPU Time = 2×10⁸ × 2 / (2×10⁹) = 4×10⁸ / 2×10⁹ = 0.2 seconds (200 ms)`

**A2:** `Speedup_max = 1 / (1 − 0.80) = 1 / 0.20 = 5×` — even with infinite processors, maximum speedup is 5×.

**A3:** 
- A: IC × 1 / 10⁹ = IC × 1 ns
- B: IC × 3 / 2×10⁹ = IC × 1.5 ns
- **Processor A is 1.5× faster** despite lower clock rate, because CPI difference dominates.

**A4:** `CPI = 0.40×1 + 0.35×4 + 0.25×2 = 0.40 + 1.40 + 0.50 = 2.30 cycles`

**A5:** If clock rate doubles, clock cycle time halves → `CPU Time = IC × CPI × (CCT/2) = Original_Time / 2`. **CPU time is halved.**
