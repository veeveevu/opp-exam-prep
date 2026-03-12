# ✅ SOLUTIONS — Detailed Step-by-Step

> **Study tip:** Try the problems yourself first! Only check solutions when stuck or after attempting.
> 
> **Partial credit reminder:** On paper exams, write the formula FIRST, then substitute, then solve.

---

# 🗃️ CACHE SOLUTIONS

## Problem 1 — Address Breakdown (Direct Mapped)

**Given:** 8KB cache, 32-byte blocks, 32-bit addresses

**a) Number of blocks:**
```
Number of blocks = Cache Size / Block Size = 8KB / 32B = 8192 / 32 = 256 blocks
```

**b) Bit breakdown:**
```
Offset bits = log₂(32) = 5 bits
Index bits  = log₂(256) = 8 bits
Tag bits    = 32 − 8 − 5 = 19 bits
```

**c) Break down address `0x00001A40`:**
```
0x00001A40 in binary:
0000 0000 0000 0000 0001 1010 0100 0000

Offset  (bits [4:0]):  0 0000 = 0x00 (byte 0 within block)
Index   (bits [12:5]): 0010 0010 = 0x22 = 34
Tag     (bits [31:13]):000 0000 0000 0000 000 = 0

Address maps to: cache index 34, tag 0
```

---

## Problem 2 — Set Associative Address Breakdown

**Given:** 64KB cache, 4-way, 64-byte blocks, 32-bit addresses

**a) Number of sets:**
```
Total blocks = 64KB / 64B = 1024 blocks
Number of sets = 1024 / 4 = 256 sets
```

**b) Bit breakdown:**
```
Offset bits = log₂(64) = 6 bits
Index bits  = log₂(256) = 8 bits
Tag bits    = 32 − 8 − 6 = 18 bits
```

**c) Address `0x0000F480` → which set?**
```
0x0000F480 = 0000 0000 0000 0000 1111 0100 1000 0000

Offset  (bits [5:0]):  00 0000 = 0
Index   (bits [13:6]): 0001 0010 = 0x12 = 18
Tag     (bits [31:14]): 0

Maps to set 18
```

---

## Problem 3 — AMAT (Single Level)

**a)**
```
AMAT = Hit Time + Miss Rate × Miss Penalty
     = 4 + 0.08 × 120
     = 4 + 9.6
     = 13.6 cycles
```

**b)**
```
Total memory cycles = 10⁸ instructions × 1 access/instruction × AMAT
                   = 10⁸ × 13.6
                   = 1.36 × 10⁹ cycles
```

---

## Problem 4 — AMAT (Two Level)

**a) Two-level AMAT:**
```
AMAT = L1_HT + L1_MR × (L2_HT + L2_MR × Mem_Penalty)
     = 2 + 0.05 × (12 + 0.30 × 200)
     = 2 + 0.05 × (12 + 60)
     = 2 + 0.05 × 72
     = 2 + 3.6
     = 5.6 cycles
```

**b) Single-level comparison:**
```
AMAT_single = 2 + 0.05 × 200 = 2 + 10 = 12 cycles

Improvement: 12 / 5.6 ≈ 2.14× faster with L2 cache
```

---

## Problem 5 — Cache Hit/Miss Trace

**Direct-mapped, 4 blocks (indices 0–3), block size = 1 word, initially empty**

Index = block_address mod 4

| Access | Block | Index | Cache State | Hit/Miss | Evicted |
|--------|-------|-------|-------------|---------|---------|
| 0 | 0 | 0 | {0:_, 1:_, 2:_, 3:_} → {0,_,_,_} | MISS | — |
| 4 | 4 | 0 | {0,_,_,_} → {4,_,_,_} | MISS | block 0 |
| 8 | 8 | 0 | {4,_,_,_} → {8,_,_,_} | MISS | block 4 |
| 0 | 0 | 0 | {8,_,_,_} → {0,_,_,_} | MISS | block 8 |
| 4 | 4 | 0 | {0,_,_,_} → {4,_,_,_} | MISS | block 0 |
| 1 | 1 | 1 | {4,_,_,_} → {4,1,_,_} | MISS | — |
| 4 | 4 | 0 | {4,1,_,_} | HIT | — |
| 8 | 8 | 0 | {4,1,_,_} → {8,1,_,_} | MISS | block 4 |
| 0 | 0 | 0 | {8,1,_,_} → {0,1,_,_} | MISS | block 8 |
| 4 | 4 | 0 | {0,1,_,_} → {4,1,_,_} | MISS | block 0 |

**Result:** 1 hit, 9 misses. Direct-mapped suffers from conflict misses (0, 4, 8 all map to index 0).

---

## Problem 6 — Write Policies

**a) Write-back + Write-allocate:**
1. Read 0: miss → load block 0 into cache → cache: {[0]=valid, dirty=0}
2. Read 1: miss → load block 1 → cache: {[0], [1]=valid}
3. Write 0 (val=42): HIT → write to cache only, set dirty bit → cache: {[0]=dirty}
4. Read 0: HIT → return 42 from cache
5. Evict block 0: dirty=1 → write block 0 (with val=42) back to memory → memory updated

**Memory writes: 1** (only on eviction)

**b) Write-through + No-write-allocate:**
1. Read 0: miss → load block into cache
2. Read 1: miss → load block
3. Write 0: HIT → write to cache AND memory simultaneously
4. Read 0: HIT → return 42
5. "Evict": block is clean → no write-back needed

**Memory writes: 1** (on write hit)
But write-through causes a memory write on every store hit — more traffic for programs with many writes.

---

## Problem 7 — Fully Associative vs Direct Mapped

Access sequence: 1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5

**a) Direct-mapped, 4 blocks (index = addr mod 4):**

| Access | Index | Cache content | H/M |
|--------|-------|---------------|-----|
| 1 | 1 | {_,1,_,_} | M |
| 2 | 2 | {_,1,2,_} | M |
| 3 | 3 | {_,1,2,3} | M |
| 4 | 0 | {4,1,2,3} | M |
| 1 | 1 | {4,1,2,3} | H |
| 2 | 2 | {4,1,2,3} | H |
| 5 | 1 | {4,5,2,3} | M (evicts 1) |
| 1 | 1 | {4,1,2,3} | M (evicts 5) |
| 2 | 2 | {4,1,2,3} | H |
| 3 | 3 | {4,1,2,3} | H |
| 4 | 0 | {4,1,2,3} | H |
| 5 | 1 | {4,5,2,3} | M (evicts 1) |

**Direct-mapped: 7 misses**

**b) Fully associative (LRU), 4 blocks:**

| Access | Cache (LRU order, left=oldest) | H/M |
|--------|-------------------------------|-----|
| 1 | {1} | M |
| 2 | {1,2} | M |
| 3 | {1,2,3} | M |
| 4 | {1,2,3,4} | M |
| 1 | {2,3,4,1} | H |
| 2 | {3,4,1,2} | H |
| 5 | {4,1,2,5} (evict 3) | M |
| 1 | {4,2,5,1} | H |
| 2 | {4,5,1,2} | H |
| 3 | {5,1,2,3} (evict 4) | M |
| 4 | {1,2,3,4} (evict 5) | M |
| 5 | {2,3,4,5} (evict 1) | M |

**Fully associative: 7 misses** (same here, but generally FA has fewer conflict misses)

**c)** In this specific access pattern, both have the same misses. Direct-mapped is simpler and faster but suffers more conflict misses with adversarial access patterns (as seen in Problem 5).

---

## Problem 8 — Performance Impact of Cache

**a) CPI without cache:**
```
Without cache, every instruction accesses memory:
CPI_no_cache = Base_CPI + memory_cycles_per_instruction
= 1 + 1 × 100 = 101
```

**b) CPI with cache:**
```
AMAT = 2 + 0.05 × 100 = 2 + 5 = 7 cycles
CPI_cache = 1 + 1 × (AMAT - 1)  ← 1 cycle is part of base pipeline
= 1 + (7-1) = 7 ... 

Actually, more precisely:
CPI = Base + memory_stalls = 1 + 1 × (0.05 × 100) = 1 + 5 = 6
(The hit time is assumed to be 0 additional cycles in the base CPI of 1)

Or if hit time adds cycles: CPI = 1 + 0.95×(2-1) + 0.05×100 
More simply: assume AMAT directly adds to base CPI:
CPI = 1 + AMAT - 1 = AMAT = 7 cycles
```

Use: **CPI_with_cache = AMAT = 7 cycles** (assuming memory is on critical path always)

**c) Speedup:**
```
Speedup = CPI_no_cache / CPI_cache = 101 / 7 ≈ 14.4×
```

Massive speedup! Cache is one of the most impactful hardware optimizations.

---

## Problem 9 — Miss Rate Changes (Reasoning)

**a) Doubling cache size:**
**Decreases miss rate.** More space means fewer capacity misses and potentially fewer conflict misses. Cold misses unchanged (still first access to each block).

**b) Doubling block size (same total cache size):**
**Initially decreases miss rate** (better spatial locality — more useful data per block), but if blocks are too large, **increases miss rate** (fewer total blocks → more conflict misses, more wasted bandwidth if spatial locality is poor).

**c) Increasing from direct-mapped to 2-way:**
**Decreases miss rate.** Direct-mapped has high conflict misses; 2-way halves number of sets but each set can hold 2 blocks, eliminating many conflict misses with only a small hit time penalty.

**d) Switching from LRU to random:**
**Slightly increases miss rate** in general (LRU better approximates optimal). For workloads with clear temporal locality, LRU is noticeably better. For random access patterns, the difference is small.

---

## Problem 10 — Full Cache Design

**Given:** 32-bit, 16KB, 8-way set associative, 64-byte blocks

**a) Number of sets:**
```
Total blocks = 16KB / 64B = 256
Sets = 256 / 8 = 32 sets
```

**b) Bit breakdown:**
```
Offset bits = log₂(64) = 6 bits
Index bits  = log₂(32) = 5 bits
Tag bits    = 32 − 5 − 6 = 21 bits
```

**c) Total storage bits:**
```
Per cache line: 1 valid + 1 dirty + 21 tag + 64×8 data = 535 bits
Total lines = 32 sets × 8 ways = 256 lines
Total = 256 × 535 = 136,960 bits ≈ 17KB of overhead for a 16KB data cache
```

**d) AMAT:**
```
AMAT = 3 + 0.03 × 200 = 3 + 6 = 9 cycles
```

---

# ⚙️ PIPELINING SOLUTIONS

## Problem 1 — Basic Timing Diagram

```
         CC1  CC2  CC3  CC4  CC5  CC6  CC7  CC8  CC9  CC10
I1: ADD   IF   ID   EX  MEM   WB
I2: SUB        IF   ID   EX  MEM   WB
I3: AND             IF   ID   EX  MEM   WB
I4: OR                   IF   ID   EX  MEM   WB
I5: LW                        IF   ID   EX  MEM   WB
I6: SW                             IF   ID   EX  MEM   WB
```

**a)** Total cycles = 5 + (6−1) = **10 cycles**

**b)** CPI = 10/6 ≈ 1.0 (approaching ideal as n grows)

---

## Problem 2 — Data Hazards (No Forwarding)

**a) Hazards:**
- I1 writes $s0 in WB (CC5). I2 reads $s0 in ID (CC3) → **RAW on $s0, 2 stalls needed**
- I2 writes $s1 in WB. I3 reads $s1 in ID → **RAW on $s1, more stalls**

**b) Stall cycles (No Forwarding):**
- Rule: WB at cycle k → available at cycle k+1. ID must happen at cycle ≥ k+1.
- I1: WB at CC5. I2 ID normally at CC3. Must delay ID to CC6 → **2 stalls for I1→I2**
- I2: WB at CC5+2stalls = CC9. I3 ID normally at CC4+2stalls = CC6. Must delay to CC10 → **4 stalls for I2→I3**

Wait, let me recalculate carefully:

Without stalls:
- I1: IF=1, ID=2, EX=3, MEM=4, WB=5
- I2: IF=2, ID=3, EX=4, MEM=5, WB=6  ← but needs $s0 in ID(CC3), I1 WB is CC5 → stall 2 cycles
- With 2 stalls, I2: IF=2, stall,stall, ID=5, EX=6, MEM=7, WB=8

Actually more standard:
- I2 ID delayed to CC6 (after I1 WB at CC5) → stall cycles: CC3, CC4 → 2 stalls
- I2 WB at CC10. I3 ID needs to be at CC11 (after I2 WB). I3 was scheduled for ID at CC3. Must delay to CC11 → many stalls.

**c) Timing diagram (approximate):**
```
         CC1  CC2  CC3  CC4  CC5  CC6  CC7  CC8  CC9  CC10 CC11 CC12
I1:       IF   ID   EX  MEM   WB
I2:            IF  [s] [s]   ID   EX  MEM   WB
I3:                 IF  [s] [s] [s] [s]    ID   EX  MEM   WB
```
(s = stall/bubble)

**d)** Total cycles ≈ 12 for 3 instructions → CPI ≈ 4.0

---

## Problem 3 — Data Hazards (With Forwarding)

**a) Forwarding resolves:**
- I1(EX result at CC3) → I2(EX at CC4): Forward from EX/MEM to EX input. **0 stalls.**
- I2(EX result at CC4) → I3(EX at CC5): Forward from EX/MEM to EX input. **0 stalls.**

**b) Stall cycles: 0** (all resolved by forwarding)

**c) Timing diagram:**
```
         CC1  CC2  CC3  CC4  CC5  CC6  CC7
I1: ADD   IF   ID   EX  MEM   WB
I2: SUB        IF   ID   EX  MEM   WB
I3: AND             IF   ID   EX  MEM   WB
```
No stalls — normal pipeline flow. Total = 7 cycles for 3 instructions, CPI ≈ 1.0.

---

## Problem 4 — Load-Use Hazard

**a) Why forwarding can't fully help:**
LW produces its value at the **end of MEM stage** (CC4 for I1). ADD needs the value at the **beginning of EX stage** (CC4 for I2, if no stall). The value isn't ready in time — even with forwarding, it arrives too late by 1 cycle.

**b) Stall cycles:** **1 stall** required.

**c) Timing diagram:**
```
         CC1  CC2  CC3  CC4  CC5  CC6  CC7  CC8
I1: LW    IF   ID   EX  MEM   WB
I2: ADD        IF   ID [stall] EX  MEM   WB
I3: SUB             IF   ID    EX  MEM   WB
```
LW data available after MEM (CC4). ADD EX happens at CC5. **1 stall is sufficient with forwarding** (MEM→EX forwarding).

**d) With I3 between I1 and I2:**
```
I1: LW  $t0, 0($s0)
I3: SUB $t3, $t4, $t5   ← unrelated instruction fills the slot
I2: ADD $t1, $t0, $t2   ← now 1 cycle apart from LW; forwarding works!
```
**No stall needed!** The compiler schedules around the hazard.

---

## Problem 5 — Branch Hazards

**a) Instructions squashed (branch resolved at end of EX, CC3):**
- I@0x104 was fetched at CC2 (IF) → must be squashed
- I@0x108 was in ID at CC3 when branch resolves → must be squashed
- **2 instructions squashed** → 2-cycle penalty

**b) Branch penalty: 2 cycles**

**c) If resolved at end of ID (CC2):**
- I@0x104 was fetched at CC2 → must be squashed
- **1 instruction squashed** → **1-cycle penalty**

---

## Problem 6 — CPI with Mixed Hazards

```
Load-use stalls:
  = load_freq × load-use_hazard_rate × 1_cycle
  = 0.25 × 0.40 × 1
  = 0.10 cycles/instruction

Branch misprediction stalls:
  = branch_freq × (1 - accuracy) × penalty
  = 0.20 × 0.10 × 2
  = 0.04 cycles/instruction

CPI = Base + load_stalls + branch_stalls
    = 1.0 + 0.10 + 0.04
    = 1.14 cycles/instruction
```

---

## Problem 7 — Complete Trace

```assembly
I1: LW  $t0, 0($s0)
I2: LW  $t1, 4($s0)
I3: ADD $t2, $t0, $t1   ← uses both I1 and I2 results
I4: SW  $t2, 8($s0)
```

**a) Hazards:**
- I1→I3: Load-use with one instruction gap (I2 in between) → forwarding works, 0 stalls
  (I1 MEM at CC4, I3 EX at CC5+stalls: need to check)
- I2→I3: Load-use with no gap → **1 stall required**
  (I2 MEM at CC5, I3 EX at CC5 without stall — conflict!)

Let me trace:
- I1: IF=1, ID=2, EX=3, MEM=4, WB=5 → $t0 available after MEM=CC4
- I2: IF=2, ID=3, EX=4, MEM=5, WB=6 → $t1 available after MEM=CC5
- I3 needs $t0 (from I1 MEM=CC4) and $t1 (from I2 MEM=CC5)
  - Without stall: I3 EX = CC5. I1 result available: MEM=CC4, forward to CC5 ✅
  - $t1 from I2: MEM=CC5, I3 EX=CC5 → same cycle! → **1 stall needed**
- With 1 stall: I3 EX = CC6. I2 MEM=CC5, forward to CC6 ✅

**b) Timing diagram:**
```
         CC1  CC2  CC3  CC4  CC5  CC6  CC7  CC8  CC9
I1: LW    IF   ID   EX  MEM   WB
I2: LW         IF   ID   EX  MEM   WB
I3: ADD             IF   ID [stall] EX  MEM   WB
I4: SW                   IF   ID    EX  MEM   WB
```

**c) Total cycles:** 9 cycles for 4 instructions

**d) CPI:** 9/4 = 2.25 (includes 1 stall cycle)

---

## Problem 8 — Structural Hazard

**a) Structural hazards occur when:**
- I1 needs MEM at CC4
- I3 needs MEM at CC6 (3 instructions later, CC=1,2,3,4,5,6 for stages)
- I4 needs MEM at CC7
- No conflict in this case — they don't need MEM simultaneously

In general: structural hazard occurs when two instructions reach MEM in the same cycle. With 4 stages between LW instructions (→ no conflict in this specific code).

**b) Standard MIPS solution:** Use **separate instruction cache** and **data cache** (or at minimum separate instruction and data memory ports). This eliminates instruction-fetch/data-access conflicts. This is the standard 5-stage MIPS design assumption.

---

# ⚡ PERFORMANCE SOLUTIONS

## Problem 1 — Iron Law Basics

**a)** 
```
CPU Time = IC × CPI × CCT = IC × CPI / Clock_Rate
         = 5×10⁸ × 2.5 / (2×10⁹)
         = 12.5×10⁸ / 2×10⁹
         = 0.625 seconds
```

**b) New compiler:**
```
IC_new = 5×10⁸ × 0.8 = 4×10⁸
CPI_new = 3
Clock rate = 2 GHz

Time_new = 4×10⁸ × 3 / (2×10⁹) = 12×10⁸ / 2×10⁹ = 0.6 seconds

Speedup = 0.625 / 0.6 ≈ 1.04

Yes! New compiler is ~4% faster. Smaller IC won out despite higher CPI.
```

**c)**
```
Time_new = 5×10⁸ × 2.0 / (2×10⁹) = 0.5 seconds
Speedup = 0.625 / 0.5 = 1.25×
```

---

## Problem 2 — CPI from Instruction Mix

**a)**
```
CPI = 0.43×1 + 0.07×10 + 0.21×3 + 0.12×3 + 0.17×2
    = 0.43 + 0.70 + 0.63 + 0.36 + 0.34
    = 2.46
```

**b) FP from 10→2 cycles:**
```
CPI_new = 0.43×1 + 0.07×2 + 0.21×3 + 0.12×3 + 0.17×2
        = 0.43 + 0.14 + 0.63 + 0.36 + 0.34 = 1.90

Speedup = 2.46 / 1.90 ≈ 1.29×
```

**c) ALU from 1→0.5 cycles:**
```
CPI_new = 0.43×0.5 + 0.07×10 + 0.21×3 + 0.12×3 + 0.17×2
        = 0.215 + 0.70 + 0.63 + 0.36 + 0.34 = 2.245

Speedup = 2.46 / 2.245 ≈ 1.10×
```

**d) FP improvement (b) is better (1.29× vs 1.10×).**

Why? Even though ALU is 43% of instructions and FP is only 7%, FP takes 10 cycles vs 1 cycle for ALU. FP contributes much more to total CPI, so improving it has bigger impact. This demonstrates Amdahl's Law: improve the bottleneck!

---

## Problem 3 — Amdahl's Law

**a)**
```
P = 0.60, S = 4

Speedup = 1 / ((1-0.60) + 0.60/4)
        = 1 / (0.40 + 0.15)
        = 1 / 0.55
        ≈ 1.82×
```

**b) Find S for 2× overall speedup:**
```
2 = 1 / (0.40 + 0.60/S)
0.40 + 0.60/S = 0.5
0.60/S = 0.1
S = 0.60/0.1 = 6×
```
Need to speed up sort by **6×** for 2× overall speedup.

**c) Maximum speedup:**
```
Speedup_max = 1 / (1 - 0.60) = 1 / 0.40 = 2.5×
```
Even infinitely fast sort → only 2.5× overall speedup. The other 40% is the bottleneck.

---

## Problem 4 — Amdahl's Parallel

**a) 2 processors:**
```
S = 1 / (0.30 + 0.70/2) = 1 / (0.30 + 0.35) = 1 / 0.65 ≈ 1.54×
```

**b) 4 processors:**
```
S = 1 / (0.30 + 0.70/4) = 1 / (0.30 + 0.175) = 1 / 0.475 ≈ 2.11×
```

**c) 8 processors:**
```
S = 1 / (0.30 + 0.70/8) = 1 / (0.30 + 0.0875) = 1 / 0.3875 ≈ 2.58×
```

**d) Infinite processors:**
```
S_max = 1 / 0.30 ≈ 3.33×
```

**e)** Speedup increases rapidly at first, then diminishes. Even with infinite processors, speedup is capped at 3.33× because 30% is sequential. This shows the fundamental limit of parallelism.

---

## Problem 5 — CPU Time with Memory Stalls

**a) Memory stall CPI:**
```
Memory stall CPI = accesses_per_instruction × miss_rate × miss_penalty
                 = 0.30 × 0.08 × 80
                 = 1.92 cycles/instruction
```

**b) Actual CPI:**
```
CPI = 1.5 + 1.92 = 3.42
```

**c) CPU time:**
```
Time = IC × CPI / Clock_Rate
     = 2×10⁸ × 3.42 / (500×10⁶)
     = 6.84×10⁸ / 5×10⁸
     = 1.368 seconds
```

**d) With 2% miss rate:**
```
Memory stall CPI = 0.30 × 0.02 × 80 = 0.48
CPI_new = 1.5 + 0.48 = 1.98

Time_new = 2×10⁸ × 1.98 / 5×10⁸ = 0.792 seconds

Speedup = 1.368 / 0.792 ≈ 1.73×
```

---

## Problem 6 — Comparing Processors

**a)** Same IC, so:
```
Time_A = IC × 1.5 / (3×10⁹) = IC × 0.5×10⁻⁹
Time_B = IC × 2.5 / (4×10⁹) = IC × 0.625×10⁻⁹

Speedup_A_over_B = Time_B / Time_A = 0.625 / 0.5 = 1.25

Processor A is 25% faster.
```

**b) MIPS:**
```
MIPS_A = Clock_Rate / (CPI × 10⁶) = 3000 / (1.5 × 1) = 2000 MIPS
MIPS_B = 4000 / (2.5 × 1) = 1600 MIPS
```

**c) MIPS is NOT reliable here** — even though A is faster, it has lower MIPS than B if B had lower CPI. In this case A happens to be both faster AND higher MIPS, but MIPS misleads when ISAs differ or instruction types differ.

---

## Problem 7 — Improvement Analysis

**a) Speedup for each option (Amdahl's Law):**

Option A (FP 5×, 20% of time):
```
S_A = 1 / ((1-0.20) + 0.20/5) = 1 / (0.80 + 0.04) = 1 / 0.84 ≈ 1.19×
```

Option B (Memory 2×, 40% of time):
```
S_B = 1 / ((1-0.40) + 0.40/2) = 1 / (0.60 + 0.20) = 1 / 0.80 = 1.25×
```

Option C (ALU 2×, 35% of time):
```
S_C = 1 / ((1-0.35) + 0.35/2) = 1 / (0.65 + 0.175) = 1 / 0.825 ≈ 1.21×
```

**b) Option B is best (1.25×)** — largest fraction of execution time improved.

**c) Combining A and B:**
```
New execution time fraction:
  FP portion: 0.20/5 = 0.04 (reduced)
  Memory portion: 0.40/2 = 0.20 (reduced)
  Remaining: 0.40 (unchanged)
  
New total relative time = 0.04 + 0.20 + 0.40 = 0.64

Combined speedup = 1 / 0.64 ≈ 1.56×
```

---

## Problem 8 — Benchmark Analysis

**a) Branch penalty CPI:**
```
= branch_freq × misprediction_rate × penalty
= 0.15 × 0.05 × 10
= 0.075 cycles/instruction
```

**b) Load-use penalty CPI:**
```
= load_freq × load_use_rate × penalty
= 0.20 × 0.05 × 1
= 0.01 cycles/instruction
```

**c) Total CPI:**
```
CPI = 2 + 0.075 + 0.01 = 2.085
```

**d) Instructions per second:**
```
Clock_rate = 2.5 GHz = 2.5×10⁹ cycles/second
Instructions/second = Clock_rate / CPI = 2.5×10⁹ / 2.085 ≈ 1.199×10⁹ ≈ 1.2 GIPS
```

---

# 💾 VIRTUAL MEMORY SOLUTIONS

## Problem 1 — Address Breakdown

**a)** 4KB = 4096 bytes → log₂(4096) = **12 offset bits**

**b)** VPN bits = 32 − 12 = **20 bits**

**c)** Number of entries = 2²⁰ = **1,048,576 entries** (1M)

**d)** Page table size = 2²⁰ × 4 = **4MB**

---

## Problem 2 — Address Translation

**System:** 4KB pages → 12 offset bits, VPN = 20 bits

**a) VA = 0x00003A4C:**
```
VPN = 0x00003A4C >> 12 = 0x00003 = 3
Offset = 0x00003A4C & 0xFFF = 0xA4C

PT[3]: PFN=7, Valid=1 ✅
PA = (7 << 12) | 0xA4C = 0x7000 | 0xA4C = 0x7A4C
```

**b) VA = 0x00000FFF:**
```
VPN = 0x00000FFF >> 12 = 0
Offset = 0xFFF

PT[0]: PFN=5, Valid=1 ✅
PA = (5 << 12) | 0xFFF = 0x5000 | 0xFFF = 0x5FFF
```

**c) VA = 0x00002000:**
```
VPN = 0x00002000 >> 12 = 2
PT[2]: Valid=0 → PAGE FAULT!
```

**d) VA = 0x00004250:**
```
VPN = 0x00004250 >> 12 = 4
Offset = 0x250

PT[4]: PFN=22=0x16, Valid=1 ✅
PA = (0x16 << 12) | 0x250 = 0x16000 | 0x250 = 0x16250
```

---

## Problem 3 — TLB Scenarios

**a) VA = 0x00010A30, VPN = 0x10:**
TLB has VPN 0x10 → PFN 0x20 → **TLB HIT**
PA = (0x20 << 12) | 0xA30 = 0x20000 | 0xA30 = **0x20A30**

**b) VA = 0x00012050, VPN = 0x12:**
TLB has VPN 0x12 → PFN 0x40 → **TLB HIT**
PA = (0x40 << 12) | 0x050 = **0x40050**

**c) VA = 0x00015000, VPN = 0x15:**
TLB does not have VPN 0x15 → **TLB MISS** → walk page table → load PFN 0x50
TLB updated: {0x10→0x20, 0x11→0x31, 0x12→0x40, 0x15→0x50}
PA = (0x50 << 12) | 0x000 = **0x50000**

**d) VA = 0x00013000, VPN = 0x13:**
TLB miss → walk page table → PTE Valid=0 → **PAGE FAULT**

---

## Problem 4 — EMAT

**a)**
```
EMAT = TLB_hit_rate × (TLB_time + Mem_time)
     + TLB_miss_rate × (TLB_time + PT_access + Mem_time)

= 0.95 × (2 + 100) + 0.05 × (2 + 100 + 100)
= 0.95 × 102 + 0.05 × 202
= 96.9 + 10.1
= 107 cycles
```

**b) 99% hit rate:**
```
EMAT = 0.99 × 102 + 0.01 × 202
     = 100.98 + 2.02
     = 103 cycles
```

**c) Improvement:**
```
Speedup = 107 / 103 ≈ 1.04 (4% improvement)
```
Small improvement because 95% hit rate was already good. Going from 95% to 99% helps at high-frequency access patterns.

---

## Problem 5 — Two-Level Page Table

**a)** L1 has 2¹⁰ = **1,024 entries**

**b)** Each L2 table has 2¹⁰ = **1,024 entries**

**c)** Process uses 0x00000000 to 0x000FFFFF (1MB):
```
VPN range: 0x00000 to 0x000FF (256 pages)
L1 index bits: top 10 bits → range 0x000 → all map to L1 entry 0
Only 1MB of VA used → only 1 L1 entry needed → 1 L2 table
```
**1 L2 page table needed**

**d)** Memory for page table:
```
L1 table: 1,024 entries × 4 bytes = 4KB (always allocated, even if mostly unused)
L2 tables: 1 × 1,024 × 4 bytes = 4KB
Total: 8KB
```

**e)** Single-level: 2²⁰ × 4 = 4MB — **500× more memory!**

---

## Problem 6 — Page Replacement

**Reference string:** 1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5
**Frames:** 3

**a) FIFO:**

| Ref | Frame 0 | Frame 1 | Frame 2 | Fault? |
|-----|---------|---------|---------|-------|
| 1 | 1 | — | — | F |
| 2 | 1 | 2 | — | F |
| 3 | 1 | 2 | 3 | F |
| 4 | 4 | 2 | 3 | F (evict 1, oldest) |
| 1 | 4 | 1 | 3 | F (evict 2) |
| 2 | 4 | 1 | 2 | F (evict 3) |
| 5 | 5 | 1 | 2 | F (evict 4) |
| 1 | 5 | 1 | 2 | H |
| 2 | 5 | 1 | 2 | H |
| 3 | 5 | 3 | 2 | F (evict 1) |
| 4 | 5 | 3 | 4 | F (evict 2) |
| 5 | 5 | 3 | 4 | H |

**FIFO: 9 page faults**

**b) LRU:**

| Ref | Frames (MRU→LRU) | Fault? |
|-----|-----------------|-------|
| 1 | {1} | F |
| 2 | {2,1} | F |
| 3 | {3,2,1} | F |
| 4 | {4,3,2} | F (evict 1) |
| 1 | {1,4,3} | F (evict 2) |
| 2 | {2,1,4} | F (evict 3) |
| 5 | {5,2,1} | F (evict 4) |
| 1 | {1,5,2} | H |
| 2 | {2,1,5} | H |
| 3 | {3,2,1} | F (evict 5) |
| 4 | {4,3,2} | F (evict 1) |
| 5 | {5,4,3} | F (evict 2) |

**LRU: 10 page faults** (worse here due to this specific pattern!)

**c)** In this case, FIFO actually performs better! LRU doesn't always beat FIFO — access patterns matter. (This is actually related to Bélády's anomaly for FIFO, but in this case LRU just happens to make bad choices.)

---

## Problem 7 — Full Translation

**System:** 16-bit VA, 512-byte pages

**a)**
```
Offset bits = log₂(512) = 9 bits
VPN bits = 16 − 9 = 7 bits
```

**b)** 2⁷ = **128 page table entries**

**c) VA = 0x2345:**
```
Binary: 0010 0011 0100 0101
Offset (9 bits): 1 0100 0101 = 0x145
VPN (7 bits): 001 0001 = 0x11
```

**d)** VPN of 0x2345 is **0x11**. TLB has VPN 0x11 → PFN 0x8 → **TLB HIT!**
```
PA = (0x8 << 9) | 0x145 = 0x1000 | 0x145 = 0x1145
```

---

## Problem 8 — 64-bit Page Table

**System:** 64-bit VA, 4KB pages, 4-level, 9+9+9+9+12 bits

**a)** VPN bits used = 9×4 = **36 bits** (48-bit effective VA, 12 for offset)

**b)** Each level: 2⁹ = **512 entries** per page table

**c) Worst-case total page table memory:**
```
Level 1: 1 table × 512 entries × 8 bytes = 4KB
Level 2: 512 tables × 512 × 8 = 2MB
Level 3: 512² tables × 512 × 8 = 1GB
Level 4: 512³ tables × 512 × 8 = 512GB

Total worst case: > 512GB (impractical, but theoretical maximum)
In practice: only allocated for actually-used regions
```

**d) A single-level page table for 64-bit would need:**
```
2^36 entries × 8 bytes = 64GB per process!
```
Multi-level tables are essential — only allocate tables for used VA regions.

---

# 🔣 ASSEMBLY SOLUTIONS

## Problem 1 — Reading Assembly

**Trace with $a0 = 4:**

| Iteration | $t0 (counter) | $v0 (sum) | Condition $a0 < $t0? |
|-----------|-------------|---------|---------------------|
| Init | 1 | 0 | 4 < 1? No → loop |
| 1 | 1 → 2 | 0+1=1 | 4 < 2? No → loop |
| 2 | 2 → 3 | 1+2=3 | 4 < 3? No → loop |
| 3 | 3 → 4 | 3+3=6 | 4 < 4? No → loop |
| 4 | 4 → 5 | 6+4=10 | 4 < 5? Yes → done |

**a)** Final: $v0 = 10, $t0 = 5

**b)** This computes **sum from 1 to n** = n×(n+1)/2 = 4×5/2 = 10 ✓

---

## Problem 2 — Writing Assembly

```assembly
# a = $s0, b = $s1, c = $s2, result = $s3

  SLT  $t0, $s0, $s1    # t0 = (a < b) ? 1 : 0
  BEQ  $t0, $zero, else  # if NOT (a < b), jump to else
  ADD  $s2, $s0, $s1     # c = a + b
  J    done
else:
  SUB  $s2, $s0, $s1     # c = a - b
done:
  ADD  $s3, $s2, $s2     # result = c * 2 = c + c
```

---

## Problem 3 — Array Access

```assembly
# $s0 = base A, $s1 = base B, $s2 = n
# result sum in $s3

  ADDI $s3, $zero, 0     # sum = 0
  ADDI $t0, $zero, 0     # i = 0
loop:
  SLT  $t1, $t0, $s2     # t1 = (i < n)
  BEQ  $t1, $zero, done  # if i >= n, exit loop
  
  SLL  $t2, $t0, 2       # t2 = i * 4 (byte offset)
  ADD  $t3, $t2, $s0     # t3 = &A[i]
  ADD  $t4, $t2, $s1     # t4 = &B[i]
  LW   $t5, 0($t3)       # t5 = A[i]
  LW   $t6, 0($t4)       # t6 = B[i]
  ADD  $s3, $s3, $t5     # sum += A[i]
  ADD  $s3, $s3, $t6     # sum += B[i]
  
  ADDI $t0, $t0, 1       # i++
  J    loop
done:
```

---

## Problem 4 — Instruction Encoding

`ADD $s1, $s2, $s3` → R-type

| Field | Value | Binary |
|-------|-------|--------|
| opcode | 0 | 000000 |
| rs ($s2) | 18 | 10010 |
| rt ($s3) | 19 | 10011 |
| rd ($s1) | 17 | 10001 |
| shamt | 0 | 00000 |
| funct | 32 | 100000 |

Binary: `000000 10010 10011 10001 00000 100000`
= `0000 0010 0101 0011 1000 1000 0010 0000`
= **0x02538820**

---

## Problem 5 — Instruction Decoding

`0x8C240004` = `1000 1100 0010 0100 0000 0000 0000 0100`

**a)** Opcode = bits [31:26] = `100011` = 35 = 0x23

**b)** Opcode 0x23 = `LW` → **I-type**

**c)** 
- rs = bits [25:21] = `00001` = register 1 = **$at** (or $1)
- rt = bits [20:16] = `00100` = register 4 = **$a0**
- immediate = bits [15:0] = `0000 0000 0000 0100` = **4**

**d)** `LW $a0, 4($at)` — Load word: $a0 = Memory[$at + 4]

---

## Problem 6 — Branch Target

**a)** Offset = 10 (positive) → branch goes **forward** in memory

**b)** Target = PC + 4 + (offset × 4) = 0x00400028 + 4 + (10 × 4) = 0x0040002C + 40 = 0x00400028 + 4 + 40 = 0x00400054

```
0x00400028 + 4 = 0x0040002C
0x0040002C + 40 = 0x0040002C + 0x28 = 0x00400054
```

Target: **0x00400054**

**c)** `BEQ $..., $..., LOOP` where LOOP is at address 0x00400054

---

## Problem 7 — Sum of Squares

```assembly
sum_squares:
  # Iterative version (n in $a0, return in $v0)
  ADDI $v0, $zero, 0     # sum = 0
  ADDI $t0, $zero, 1     # i = 1
loop:
  SLT  $t1, $a0, $t0     # t1 = (n < i)
  BNE  $t1, $zero, done  # if n < i, exit (i > n)
  MUL  $t2, $t0, $t0     # t2 = i*i  (use MUL instruction)
  ADD  $v0, $v0, $t2     # sum += i*i
  ADDI $t0, $t0, 1       # i++
  J    loop
done:
  JR   $ra               # return
```

*(Note: MIPS MUL writes to LO/HI registers; for simplicity use MULT followed by MFLO, or use ADD loops)*

---

## Problem 8 — Recursive Factorial

```assembly
factorial:
  # n in $a0, returns in $v0
  # Save $ra and $a0 on stack
  ADDI $sp, $sp, -8
  SW   $ra, 4($sp)       # save return address
  SW   $a0, 0($sp)       # save n

  # Base case: if n <= 1, return 1
  SLTI $t0, $a0, 2       # t0 = (n < 2) i.e., n <= 1
  BEQ  $t0, $zero, recurse

  # Base case: n <= 1
  ADDI $v0, $zero, 1     # return 1
  J    done

recurse:
  ADDI $a0, $a0, -1      # a0 = n - 1
  JAL  factorial         # v0 = factorial(n-1)
  LW   $a0, 0($sp)       # restore n
  MUL  $v0, $a0, $v0    # v0 = n * factorial(n-1)
  # (Use MULT $a0, $v0; MFLO $v0 for proper MIPS)

done:
  LW   $ra, 4($sp)       # restore return address
  ADDI $sp, $sp, 8       # restore stack pointer
  JR   $ra               # return
```
