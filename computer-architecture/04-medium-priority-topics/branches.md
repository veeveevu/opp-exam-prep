# 🔀 Branches — Study Guide

> 🟢 **MEDIUM PRIORITY** — How branches affect program flow, pipeline, and instruction encoding.

---

## 1. Core Concepts

- **Branches** change the flow of program execution based on a condition or unconditionally.
- In MIPS: **conditional** branches (`BEQ`, `BNE`) and **unconditional** jumps (`J`, `JAL`, `JR`).
- Branch instructions cause **control hazards** in pipelines because the next PC is unknown until the branch is resolved.
- **Branch delay slot:** The instruction after a branch always executes (MIPS convention) — compiler fills it with useful work.
- **Branch encoding** uses a signed PC-relative offset (for `BEQ`/`BNE`) or absolute address (for `J`).

---

## 2. Branch Types in MIPS

| Instruction | Type | Target Calculation |
|-------------|------|-------------------|
| `BEQ rs, rt, label` | Conditional | PC+4 + (sign_extend(offset) × 4) |
| `BNE rs, rt, label` | Conditional | PC+4 + (sign_extend(offset) × 4) |
| `J target` | Unconditional | (PC[31:28] \| target << 2) |
| `JAL target` | Unconditional (call) | Same as J; $ra = PC+4 |
| `JR rs` | Indirect | PC = rs |
| `JALR rs` | Indirect (call) | PC = rs; $ra = PC+4 |

---

## 3. Branch Encoding

### BEQ/BNE (I-type, PC-relative)
```
 31      26  25   21  20   16  15                    0
 ┌────────┬──────┬──────┬──────────────────────────┐
 │ opcode │  rs  │  rt  │   offset (words, signed)  │
 └────────┴──────┴──────┴──────────────────────────┘

Target = PC + 4 + (SignExtend(offset) × 4)
Range: ±2^15 instructions from current PC
```

### J (J-type, pseudo-direct)
```
 31      26  25                                    0
 ┌────────┬────────────────────────────────────────┐
 │ opcode │           target (26 bits)              │
 └────────┴────────────────────────────────────────┘

Target address = {PC[31:28], target[25:0], 00}
Range: Anywhere within current 256MB region
```

---

## 4. Branch Delay Slot

In MIPS, the instruction immediately after a branch **always executes** before the branch takes effect:

```assembly
BEQ $s0, $zero, label   # branch instruction
ADD $t0, $t1, $t2       # ALWAYS EXECUTES (delay slot)
                         # (even if branch is taken!)
label:
  ...
```

**Compiler strategy:** Put a useful instruction in the delay slot (one that would execute anyway). If no useful instruction exists, put NOP.

---

## 5. Control Hazard Review

```
Pipeline without delay slot:
  IF     ID     EX    MEM    WB
BEQ:    IF     ID     EX    (branch resolved here)
Next:         IF     ID     ← must stall/flush!

With delayed branch (branch resolved in ID, 1-cycle delay):
BEQ:    IF     ID    (resolved)
Slot:          IF    (always executes)
Target:               IF    ...
```

---

## 6. Common Exam Questions

### "What is a branch delay slot and why is it used?"
A delay slot is the instruction after a branch that always executes before the branch takes effect. It exists because of pipeline timing — by the time the branch is fetched and decoded, the next instruction is already being fetched. Rather than waste that cycle (stall), MIPS defines it as part of the branch and executes it. The compiler fills it with useful work.

### "Calculate the branch target address"
1. Get current PC
2. Add 4 (next instruction)
3. Sign-extend the 16-bit offset to 32 bits
4. Multiply by 4 (convert instruction count to bytes)
5. Add to PC+4

---

## 7. Quick Self-Test

**Q1:** A `BEQ` instruction is at address 0x00400010. The offset field is 5. What is the branch target?

**Q2:** Why can't a simple `J` instruction reach any address in memory?

**Q3:** What fills a branch delay slot if no useful instruction is available?

---

### Answers

**A1:** Target = 0x00400010 + 4 + (5 × 4) = 0x00400014 + 0x14 = **0x00400028**

**A2:** The J-type instruction only has 26 bits for the target. Combined with the upper 4 bits of the PC (28 bits total, shifted left 2) it covers a 256MB region, not all 4GB of address space. For far jumps, load the address into a register and use `JR`.

**A3:** A **NOP** (no-operation) instruction — in MIPS, this is encoded as `SLL $zero, $zero, 0` (all zeros), which has no effect.
