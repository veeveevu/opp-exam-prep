# ⚙️ Pipelining Practice Problems

> Work through these on paper. Draw timing diagrams — you'll draw them on the exam!

---

## Problem 1 — Basic Pipeline Timing Diagram

A 5-stage pipeline (IF, ID, EX, MEM, WB). No hazards.

**Draw the timing diagram for these 6 instructions:**
```assembly
I1: ADD $s0, $t0, $t1
I2: SUB $s1, $s2, $t3
I3: AND $s2, $s4, $s5
I4: OR  $s3, $t6, $t7
I5: LW  $s4, 100($t0)
I6: SW  $s5, 0($s1)
```

**Questions:**
- a) How many total cycles are needed?
- b) What is the CPI?

---

## Problem 2 — Data Hazards (No Forwarding)

Assume **no forwarding**. A register written in WB stage is available the next cycle.

```assembly
I1: ADD $s0, $t0, $t1   # writes $s0 in WB
I2: SUB $s1, $s0, $t2   # reads $s0 in ID
I3: AND $s2, $s1, $t3   # reads $s1 in ID
```

**a)** Identify all RAW hazards.

**b)** How many stall cycles are needed (without forwarding)?

**c)** Draw the pipeline timing diagram with stalls.

**d)** What is the CPI for this 3-instruction sequence?

---

## Problem 3 — Data Hazards (With Forwarding)

Same code as Problem 2, but now **forwarding is enabled**.

```assembly
I1: ADD $s0, $t0, $t1
I2: SUB $s1, $s0, $t2
I3: AND $s2, $s1, $t3
```

**a)** Which hazards can be resolved with forwarding?

**b)** How many stall cycles are needed?

**c)** Draw the pipeline timing diagram.

---

## Problem 4 — Load-Use Hazard

```assembly
I1: LW  $t0, 0($s0)   # load from memory
I2: ADD $t1, $t0, $t2  # uses loaded value immediately
I3: SUB $t3, $t4, $t5  # no dependency
```

**a)** Why can't forwarding fully eliminate the hazard between I1 and I2?

**b)** How many stall cycles are required?

**c)** Draw the timing diagram.

**d)** If a compiler inserts I3 between I1 and I2 (scheduling), does the hazard still exist?

---

## Problem 5 — Branch Hazards

A 5-stage pipeline resolves branches at the **end of EX stage**. Assume the **not-taken** prediction policy.

```assembly
0x100: BEQ $s0, $zero, TARGET  # branch (taken)
0x104: ADD $t0, $t1, $t2       # next sequential
0x108: SUB $t3, $t4, $t5       # ...
0x10C: ...
TARGET:
0x200: LW  $s1, 0($s2)         # branch target
```

**a)** If the branch IS taken, which instructions are squashed?

**b)** What is the branch penalty (number of wasted cycles)?

**c)** How would the penalty differ if branches were resolved at the end of ID?

---

## Problem 6 — CPI Calculation with Mixed Hazards

A pipeline processes a program with:
- 25% load instructions; 40% of loads are immediately followed by a dependent instruction
- 20% branch instructions; 90% branch prediction accuracy; 2-cycle penalty for misprediction
- Base CPI = 1 (with forwarding for non-load hazards)

**Calculate the actual CPI.**

---

## Problem 7 — Complete Pipeline Trace

Trace the following code through a 5-stage pipeline **with forwarding**:

```assembly
I1: LW  $t0, 0($s0)    # load
I2: LW  $t1, 4($s0)    # load
I3: ADD $t2, $t0, $t1  # uses both loaded values
I4: SW  $t2, 8($s0)    # store result
```

**a)** Identify all hazards.

**b)** Draw the timing diagram (including any stalls).

**c)** How many total cycles does this sequence take?

**d)** What is the CPI?

---

## Problem 8 — Structural Hazard

A pipeline has only **one memory unit** (not separate instruction and data memories).

```assembly
I1: LW $t0, 0($s0)   # needs memory in MEM stage
I2: ADD $t1, $t2, $t3 # no memory needed
I3: LW $t4, 4($s0)   # needs memory in MEM stage
I4: SW $t5, 8($s0)   # needs memory in MEM stage
```

**a)** When do structural hazards occur if instructions issue every cycle?

**b)** What is the standard solution in MIPS to avoid this structural hazard?

---

*See [SOLUTIONS.md](SOLUTIONS.md) for complete solutions.*
