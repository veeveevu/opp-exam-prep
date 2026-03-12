# ⚙️ Pipelining — Complete Study Guide

> 🔴 **CRITICAL TOPIC** — Pipeline hazards, timing diagrams, and forwarding are almost always tested.

---

## 1. Core Concepts

- **Pipelining** overlaps the execution of multiple instructions by dividing execution into stages. Like an assembly line — each station processes a different car simultaneously.
- **5-stage pipeline (classic MIPS):** IF → ID → EX → MEM → WB
- **Ideal CPI = 1** — one instruction completes every clock cycle (in steady state).
- **Hazards** are situations where the next instruction can't execute in its expected cycle.
  - **Data hazards:** Instruction needs a result not yet computed
  - **Control hazards:** Branch changes the PC unexpectedly
  - **Structural hazards:** Two instructions need the same hardware
- **Forwarding (bypassing)** routes computed values directly from later pipeline stages back to earlier ones — eliminates most data hazard stalls.
- **Load-use hazard** is the one stall forwarding can't fully eliminate — always 1 bubble.
- **Branch prediction** reduces control hazard penalties.

---

## 2. Key Formulas & Calculations

### Basic Pipeline Performance
```
Cycles for n instructions (k-stage, no stalls):
  Total cycles = k + (n − 1)

CPI_ideal = 1

CPI_actual = 1 + stall_cycles_per_instruction
```

### Pipeline Speedup
```
Speedup = Time_without_pipeline / Time_with_pipeline
        ≈ k (ideal, with many instructions)
        
Real speedup = k / (1 + stall_fraction × k)
```

### CPI with Stalls
```
CPI = 1 + (load_use_freq × 1) + (branch_freq × branch_penalty)
```

### Branch Penalty
```
Branch penalty = cycles wasted on misprediction
(typically 1–3 cycles in 5-stage, more in deep pipelines)

CPI_penalty = branch_freq × (1 − prediction_accuracy) × branch_penalty_cycles
```

---

## 3. Visual Diagrams

### 5 Pipeline Stages
```
 IF        ID        EX        MEM       WB
 ┌──────┐  ┌──────┐  ┌──────┐  ┌──────┐  ┌──────┐
 │Fetch │→ │Decode│→ │ ALU  │→ │ Mem  │→ │ Reg  │
 │Instr │  │+Regs │  │ Exec │  │Access│  │Write │
 └──────┘  └──────┘  └──────┘  └──────┘  └──────┘
```

### Timing Diagram — No Hazards (5 instructions)
```
         CC1  CC2  CC3  CC4  CC5  CC6  CC7  CC8  CC9
I1: ADD   IF   ID   EX  MEM   WB
I2: SUB        IF   ID   EX  MEM   WB
I3: AND             IF   ID   EX  MEM   WB
I4: OR                   IF   ID   EX  MEM   WB
I5: SLT                       IF   ID   EX  MEM   WB
```
**Result:** 5 instructions complete in 9 cycles. Last instruction done at CC9.

### Timing Diagram — RAW Hazard with Stall (no forwarding)
```
         CC1  CC2  CC3  CC4  CC5  CC6  CC7  CC8  CC9  CC10
I1: LW r1  IF   ID   EX  MEM   WB
I2: ADD r2,r1  IF   ID  stall stall  EX  MEM   WB
I3: SUB         IF  stall stall  ID   EX  MEM   WB
```
`LW` completes WB at CC5. `ADD` needs r1 in ID stage → stalls 2 cycles.

### Timing Diagram — RAW with Forwarding
```
         CC1  CC2  CC3  CC4  CC5  CC6  CC7  CC8
I1: ADD r1,r2,r3   IF   ID   EX  MEM   WB
I2: SUB r4,r1,r5        IF   ID   EX  MEM   WB
                                  ↑
                         Forwarded: r1 from EX of I1 to EX of I2
```
No stall needed — forwarding paths route the result.

### Load-Use Hazard (1 stall even with forwarding)
```
         CC1  CC2  CC3  CC4  CC5  CC6  CC7  CC8  CC9
I1: LW r1      IF   ID   EX  MEM   WB
                          ↓    ↓
I2: ADD r2,r1  *    IF   ID  ---   EX  MEM   WB
                              ↑
                    1-cycle stall (bubble) required!
```
LW produces r1 at end of MEM (CC4). ADD needs r1 at start of EX (CC4). One cycle too early → must stall.

### Forwarding Paths Diagram
```
          IF    ID    EX    MEM    WB
               ┌──┐  ┌──┐  ┌──┐  ┌──┐
               │RF│  │ALU│  │DM│  │RF│
               └──┘  └──┘  └──┘  └──┘
                       ↑      │
                       │◄─────┘  EX/MEM forwarding (ALU→ALU)
                       │
                       │◄──────────┘  MEM/WB forwarding (Mem→ALU)
```

---

## 4. Worked Examples

### Example 1: Count Stall Cycles
**Code (no forwarding):**
```assembly
ADD r1, r2, r3    # writes r1 in WB (CC5)
SUB r4, r1, r5    # reads r1 in ID (CC3) — needs it in CC5: 2 stalls
AND r6, r4, r7    # reads r4 in ID (CC4+2stall=CC6) — r4 written CC7: 1 stall
```

With no forwarding:
- `ADD` writes r1 in cycle CC5
- `SUB` reads r1 in ID. Without forwarding: SUB's ID must happen AFTER CC5 (i.e., CC6) → 2 stall cycles
- `AND` reads r4 in ID. SUB writes r4 in cycle CC5+2stalls+4=CC9. AND's ID normally CC4+2stalls=CC6 → needs wait until CC9+1=... let me trace carefully.

**With forwarding:**
- `ADD r1`: WB at CC5. EX at CC3. → forwards from EX/MEM register to SUB's EX (CC4). **0 stalls.**
- `SUB r4`: EX at CC4. → forwards to AND's EX (CC5). **0 stalls.**
- Total stalls = 0

### Example 2: Load-Use Hazard
```assembly
LW  r1, 0(r2)    # I1: loads r1 from memory
ADD r3, r1, r4   # I2: uses r1 — ONE stall required
```

**Pipeline trace with forwarding:**
```
      CC1  CC2  CC3  CC4  CC5  CC6  CC7
I1:    IF   ID   EX  MEM   WB
I2:         IF   ID  stall  EX  MEM   WB
```
LW produces r1 at end of MEM (CC4). ADD needs r1 at start of EX (CC5). Forwarding works! → 1 stall is sufficient.

### Example 3: Branch Control Hazard
```assembly
BEQ r1, r2, LABEL   # Branch resolved in EX (CC3)
I2:                  # Already fetched (IF in CC2) — must flush!
I3:                  # Already in decode (ID in CC3) — must flush!
```
If branch resolved in EX: **2 instructions flushed** (2-cycle branch penalty).

To reduce: Move branch resolution to ID stage → **1-cycle penalty**.

---

## 5. Common Exam Questions

### "Draw a pipeline timing diagram for the following instructions"
**Steps:**
1. Start I1 at IF in CC1
2. Each subsequent instruction starts one cycle later
3. Identify hazards — where does the pipeline need to stall or forward?
4. Insert stalls (bubbles) where needed
5. Label each stage

### "Identify all hazards in the following code"
**Approach:**
- Look for each instruction: what registers does it READ?
- Find prior instructions that WRITE those registers
- Check if write completes before read needs it (considering forwarding)
- Load-use = always 1 stall; other RAW = 0 stalls with forwarding; branches = check policy

### "Calculate the CPI for a program with the following characteristics"
```
Base CPI = 1
Load-use stalls = load_freq × 1 (each LW followed by use = 1 extra cycle)
Branch penalty = branch_freq × penalty_cycles × (1 − accuracy)

CPI_actual = 1 + load_stalls + branch_penalty_contribution
```

### "Explain what forwarding is and how it works"
**Answer:**
- Forwarding routes a computed result from a later pipeline stage (EX or MEM) directly to an earlier stage (EX input) via bypass paths
- Instead of waiting for WB to write the register file, the result is fed directly from the pipeline register
- Reduces stalls for most RAW hazards from 2 cycles to 0 cycles
- Cannot help load-use hazard (data not available until after MEM stage)

---

## 6. Tricky Points

### Load-use is unique
Among all data hazards, only load-use requires a stall even with forwarding. The compiler can often reorder instructions to hide this stall (loop optimization).

### Branch prediction and penalties
- **Not taken prediction:** Always assume branch not taken; if taken, flush 1–3 instructions
- **Taken prediction:** Always assume branch taken; need BTB to know target address
- **2-bit predictor:** Requires 2 consecutive mispredictions to change prediction — better for loops

### Structural hazards in basic 5-stage MIPS
The classic 5-stage MIPS avoids most structural hazards by design:
- Separate instruction memory (IF) and data memory (MEM)
- Register file supports one write + two reads simultaneously

### WAR and WAW hazards in in-order pipelines
In a simple in-order 5-stage pipeline:
- WAR hazards don't occur (instructions complete in order)
- WAW hazards rarely occur
- These become important in out-of-order and superscalar processors

### Pipeline "depth" and speedup
Deeper pipelines allow higher clock frequencies (simpler per-stage logic), but:
- Branch misprediction penalty grows (more stages to flush)
- Hazard penalties are larger
- There's a sweet spot (modern CPUs: ~14–20 stages)

---

## 7. Quick Self-Test

**Q1:** In a 5-stage pipeline with forwarding, how many stall cycles does a `LW` followed immediately by `ADD` require?

**Q2:** Name the three types of data hazards.

**Q3:** In a pipeline timing diagram, how many cycles do 8 instructions take with no hazards in a 5-stage pipeline?

**Q4:** What is the branch penalty if branch outcomes are resolved at the end of EX stage in a 5-stage pipeline?

**Q5:** CPI = 1.2, load instructions = 20% of code, 30% of loads are followed immediately by a dependent instruction. What is the base CPI?

---

### Answers to Self-Test

**A1:** **1 stall cycle** — forwarding can't route LW's result until after MEM stage, but ADD needs it at EX stage.

**A2:** **RAW** (Read After Write), **WAR** (Write After Read), **WAW** (Write After Write). RAW is the most common and important.

**A3:** `k + (n−1) = 5 + (8−1) = 12 cycles`

**A4:** Branch resolved at end of EX (CC3). Instructions fetched at CC2 (1 wasted) and CC3 (1 wasted) = **2 cycles penalty** (2 instructions flushed).

**A5:** CPI = Base + load_stalls → 1.2 = Base + 0.20 × 0.30 × 1 = Base + 0.06 → **Base CPI = 1.14**
