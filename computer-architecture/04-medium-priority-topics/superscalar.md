# 🚀 Superscalar Processors — Study Guide

> 🟢 **MEDIUM PRIORITY** — Understand the concept and limitations; IPC > 1 is the key idea.

---

## 1. Core Concepts

- A **superscalar** processor issues **more than one instruction per clock cycle** using multiple functional units.
- **IPC (Instructions Per Cycle) > 1** — beyond what a single pipeline achieves.
- Requires **out-of-order execution** to find enough independent instructions to fill multiple issue slots.
- **Key hardware:** Multiple execution units (ALUs, FPUs, load/store units), reorder buffer, reservation stations.
- **Tomasulo's algorithm** is the classic dynamic scheduling algorithm for out-of-order execution.
- **Register renaming** eliminates WAR and WAW hazards (only RAW hazards remain).

---

## 2. Superscalar vs In-Order Pipeline

| Feature | In-Order Pipeline | Superscalar (OOO) |
|---------|------------------|-------------------|
| Instructions/cycle | 1 | 2, 4, 8 (issue width) |
| Execution order | Program order | Any safe order |
| Hardware complexity | Low | High |
| IPC | ≤ 1 | > 1 (theoretically) |
| Power consumption | Low | High |
| Examples | Simple embedded | Intel Core, AMD Zen |

---

## 3. Key Components for Out-of-Order Execution

```
┌─────────────────────────────────────────────────────────┐
│                  Superscalar Pipeline                    │
│                                                          │
│  Fetch → Decode → Rename → Issue → Execute → Commit     │
│                      │       │                           │
│                  Register  Reservation  Reorder          │
│                  Rename    Stations     Buffer (ROB)     │
└─────────────────────────────────────────────────────────┘
```

| Component | Purpose |
|-----------|---------|
| **Reservation Stations** | Hold instructions waiting for operands |
| **Reorder Buffer (ROB)** | Ensures in-order commit for correctness/exceptions |
| **Register Renaming** | Eliminates WAR/WAW hazards by using physical registers |
| **Multiple ALUs** | Execute independent instructions simultaneously |
| **Common Data Bus (CDB)** | Broadcasts results to all waiting reservation stations |

---

## 4. Tomasulo's Algorithm (Key Ideas)

1. Instructions issued in order to reservation stations
2. Each instruction waits in its reservation station until **all operands are available**
3. When operands are ready, instruction executes on the next available functional unit
4. Result is broadcast on the **Common Data Bus (CDB)** — other stations watching for this value receive it
5. Instructions **commit** in program order (using ROB) to maintain correct state

**Register renaming:** Instead of writing to architectural register directly, write to a physical register. Tag tracks which physical register holds the latest value. Eliminates WAR/WAW — different instructions using the "same" register actually use different physical registers.

---

## 5. Performance Limits of Superscalar

```
Ideal IPC = issue_width (e.g., 4-wide = IPC 4)

Actual IPC << Ideal due to:
  1. True data dependencies (RAW) — limit instruction-level parallelism
  2. Cache misses — stall many instructions
  3. Branch mispredictions — flush in-flight instructions
  4. Resource conflicts — not enough functional units
  5. Memory ordering constraints
```

---

## 6. Quick Self-Test

**Q1:** A 4-wide superscalar processor achieves IPC=2.5. What is its CPI?

**Q2:** What is the purpose of register renaming in out-of-order execution?

**Q3:** Why must a superscalar processor commit instructions in program order even if they execute out of order?

---

### Answers

**A1:** CPI = 1/IPC = 1/2.5 = **0.4 cycles per instruction**

**A2:** Register renaming eliminates WAR (Write After Read) and WAW (Write After Write) hazards by mapping each new write to a fresh physical register. Only true RAW data dependencies remain, allowing more instructions to execute in parallel.

**A3:** To maintain correct behavior for exceptions and interrupts — if an exception occurs, the processor must know exactly which instructions have "committed" and which haven't, and be able to restore the correct architectural state. Out-of-order commit would make exception handling incorrect.
