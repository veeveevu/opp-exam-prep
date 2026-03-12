# 🔮 Branch Prediction — Study Guide

> 🟡 **HIGH PRIORITY** — State machine diagrams for 1-bit and 2-bit predictors are classic exam material.

---

## 1. Core Concepts

- **Branch prediction** allows the CPU to keep the pipeline full by guessing the outcome of a branch before it's resolved.
- Without prediction, the CPU stalls waiting for branch resolution — costly in deep pipelines.
- **Prediction accuracy** directly impacts CPI: misprediction = pipeline flush + penalty.
- **Static prediction:** Fixed strategy, doesn't adapt to runtime behavior.
- **Dynamic prediction:** Learns from past behavior using hardware tables.
- The **2-bit saturating counter** is the canonical dynamic predictor — robust to single outlier branch outcomes.
- The **Branch Target Buffer (BTB)** caches branch target addresses for taken branches.

---

## 2. Key Formulas

```
CPI_with_branches = CPI_base + branch_freq × misprediction_rate × penalty_cycles

Branch penalty impact = branch_freq × (1 − accuracy) × penalty

Example:
  20% branches, 90% prediction accuracy, 5-cycle penalty
  Penalty contribution = 0.20 × (1−0.90) × 5 = 0.20 × 0.10 × 5 = 0.10 cycles/instruction
```

---

## 3. Branch Prediction Strategies

### Static Strategies (no hardware learning)

| Strategy | Rule | Good For |
|----------|------|---------|
| **Always Not Taken** | Predict branch never taken | Sequential code |
| **Always Taken** | Predict branch always taken | Loops (backward branches) |
| **BTFN** (Backward Taken, Forward Not Taken) | Backward branch = taken; forward = not taken | Best static strategy |

**BTFN rationale:** Backward branches are usually loop back-edges (taken most of the time). Forward branches are usually if-else (not taken ~60% of the time).

### Dynamic Strategies

#### 1-Bit Predictor
- One bit per branch entry in the Branch History Table (BHT)
- **1** = last outcome was taken → predict taken
- **0** = last outcome was not taken → predict not taken

**Problem with 1-bit:** In a loop that runs 10 times, the predictor mispredicts on:
- First iteration (starting from wrong state)
- Last iteration (branches not taken when exiting loop)
= 2 mispredictions per loop execution

#### 2-Bit Saturating Counter (Most Important!)
- 4 states: Strongly Not Taken (SNT), Weakly Not Taken (WNT), Weakly Taken (WT), Strongly Taken (ST)
- Requires **two consecutive** mispredictions to change prediction direction
- Much more stable for loops

---

## 4. Visual Diagrams

### 1-Bit Predictor State Machine
```
         Taken          Not Taken
    ┌─────────┐        ┌─────────┐
    │         │        │         │
    ▼         │        ▼         │
  ┌───┐ Taken │      ┌───┐ Not   │
  │ 1 │───────┘      │ 0 │ Taken │
  │(T)│              │(NT│───────┘
  └───┘              └───┘
    │ Not Taken         │ Taken
    └──────────────────►│
                        └──►(0)
```

More clearly:
```
State 1 (Predict Taken):
  - Branch Taken → stay in State 1
  - Branch Not Taken → go to State 0

State 0 (Predict Not Taken):
  - Branch Not Taken → stay in State 0
  - Branch Taken → go to State 1
```

### 2-Bit Saturating Counter State Machine ⭐
```
     Not Taken          Not Taken         Not Taken
   ┌────────────┐     ┌────────────┐
   ▼            │     ▼            │
 ┌─────┐      ┌─────┐           ┌─────┐      ┌─────┐
 │ SNT │─────►│ WNT │──────────►│  WT │─────►│  ST │
 │  00 │      │  01 │           │  10 │      │  11 │
 └─────┘      └─────┘           └─────┘      └─────┘
   Predict       Predict          Predict       Predict
   Not Taken    Not Taken          Taken         Taken
       ▲            │                 ▲              │
       └────────────┘ Not Taken       └──────────────┘
                                            Taken

Transitions:
  SNT → WNT: branch taken
  WNT → SNT: branch not taken (saturate at SNT)
  WNT → WT:  branch taken
  WT → WNT:  branch not taken
  WT → ST:   branch taken
  ST → WT:   branch not taken (saturate at ST)
  ST stays ST on taken; SNT stays SNT on not taken
```

**Key point:** Need 2 consecutive opposite outcomes to flip the prediction.

### Branch History Table (BHT) Structure
```
 Branch PC (low bits)
        │
        ▼
 ┌──────┬────────────────────┐
 │ Entry│  Counter (1 or 2b) │
 ├──────┼────────────────────┤
 │  00  │        10  (WT)    │
 │  01  │        11  (ST)    │
 │  10  │        00  (SNT)   │
 │  11  │        10  (WT)    │
 └──────┴────────────────────┘
```

---

## 5. Worked Examples

### Example 1: 2-Bit Predictor Trace
**Branch taken pattern:** T, T, T, NT, T, T, NT, NT, T
**Start state:** SNT (00)

| Outcome | State Before | Prediction | Correct? | State After |
|---------|-------------|------------|---------|------------|
| T  | SNT (00) | NT | ❌ Miss | WNT (01) |
| T  | WNT (01) | NT | ❌ Miss | WT  (10) |
| T  | WT  (10) | T  | ✅ Hit  | ST  (11) |
| NT | ST  (11) | T  | ❌ Miss | WT  (10) |
| T  | WT  (10) | T  | ✅ Hit  | ST  (11) |
| T  | ST  (11) | T  | ✅ Hit  | ST  (11) |
| NT | ST  (11) | T  | ❌ Miss | WT  (10) |
| NT | WT  (10) | T  | ❌ Miss | WNT (01) |
| T  | WNT (01) | NT | ❌ Miss | WT  (10) |

Mispredictions: 6 out of 9 = 33% miss rate. (Poor pattern — lots of alternating)

### Example 2: Loop Branch Prediction
**Loop that runs 10 times (taken 9 times, not taken once at exit)**

**1-bit predictor (starting at NT state):**
- Iter 1: predict NT, branch T → **miss**, update to T
- Iter 2–9: predict T, branch T → **hit** (8 hits)
- Iter 10 (exit): predict T, branch NT → **miss**, update to NT
- Total: 2 misses per loop execution

**2-bit predictor (starting at SNT):**
- Iter 1: predict NT, T → **miss** → WNT
- Iter 2: predict NT, T → **miss** → WT  
- Iter 3–9: predict T, T → **hit** (7 hits)
- Iter 10: predict T, NT → **miss** → WT
- Total: 3 misses (slightly worse start but same ending — for longer loops, much better)

For a loop running 100 times: 1-bit has 2 misses (2%), 2-bit has 3 misses (3%) — comparable. But 2-bit handles **irregular** patterns much better.

---

## 6. Common Exam Questions

### "Draw the state diagram for a 2-bit predictor"
Draw the 4 states (SNT, WNT, WT, ST), arrows for Taken/Not-Taken transitions between each state. Always saturate at the ends.

### "Trace through a sequence of branch outcomes using a 2-bit predictor"
Use the table format above: State | Prediction | Outcome | Correct? | New State

### "What is the advantage of a 2-bit predictor over a 1-bit predictor?"
A 2-bit predictor requires two consecutive mispredictions to change prediction. This makes it more stable — a single outlier branch outcome (like a loop exit) doesn't immediately change the prediction for the next loop.

### "Calculate the CPI impact of branch misprediction"
```
CPI_penalty = branch_frequency × (1 − accuracy) × penalty_cycles
```

---

## 7. Tricky Points

### The predictor indexes by PC, not instruction identity
Multiple branches at different memory locations can hash to the same BHT entry → aliasing. This is a source of mispredictions.

### BTB is separate from BHT
- **BHT (Branch History Table):** Predicts *whether* branch is taken
- **BTB (Branch Target Buffer):** Predicts *where* the branch goes (target address)
Both are needed for efficient branch prediction.

### Indirect branches are harder to predict
Direct branches always go to the same target. Indirect branches (via register, like function pointers) need a different predictor (ITTAGE, indirect BTB).

### Return address stack (RAS)
For function returns (`JR $ra`), a special **return address stack** predicts the target — much more accurate than BTB for returns.

---

## 8. Quick Self-Test

**Q1:** A branch is taken 90% of the time. What is the best static prediction strategy? What accuracy does it achieve?

**Q2:** Starting from state WT (Weakly Taken), trace through: T, T, NT, NT, NT. What is the final state?

**Q3:** Branch frequency = 15%, misprediction rate = 10%, penalty = 4 cycles. What is the CPI contribution from branch mispredictions?

**Q4:** What is the main advantage of a 2-bit predictor vs a 1-bit predictor?

**Q5:** A loop runs 50 iterations. Using a 1-bit predictor starting in NT state, how many mispredictions occur?

---

### Answers

**A1:** "Always Taken" — achieves **90% accuracy** (predicts correctly on all taken outcomes).

**A2:** WT → ST (T) → ST (T) → WT (NT) → WNT (NT) → SNT (NT). **Final state: SNT**

**A3:** `CPI_contribution = 0.15 × 0.10 × 4 = 0.06 cycles/instruction`

**A4:** A 2-bit predictor needs **two consecutive wrong predictions** to change its prediction direction, making it more robust to single outlier outcomes (like the last iteration of a loop).

**A5:** Taken 49 times, not taken once (exit).
- Start at NT: Iter 1: predict NT, T → **miss**, update to T
- Iter 2–49: predict T, T → **hit** (48 hits)
- Iter 50: predict T, NT → **miss**
- **Total: 2 mispredictions** (regardless of loop length for 1-bit predictor)
