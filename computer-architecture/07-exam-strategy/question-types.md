# 🎯 Question Types — How to Approach Each Type

> Paper exams test different skills. Each question type has a strategy.

---

## Type 1: EXPLAIN/DESCRIBE Questions

**Trigger words:** "Explain how...", "Describe...", "What is...", "How does... work?"

**What the examiner wants:**
- Clear understanding, not memorized definitions
- Logical sequence of steps
- Why something works, not just what it does

### ✅ Strategy Framework:
1. **Define** the concept in 1–2 sentences
2. **Explain the mechanism** step by step (numbered list)
3. **Give a concrete example** (even invented numbers)
4. **State why it matters** or what problem it solves

### Example:
> "Explain how a TLB works."

❌ **Bad answer:** "TLB stands for Translation Lookaside Buffer. It stores translations."

✅ **Good answer:**
"The TLB (Translation Lookaside Buffer) is a small, fully-associative hardware cache that stores recent virtual-to-physical address mappings.

When the CPU generates a virtual address, it first checks the TLB:
1. The VPN is compared in parallel against all TLB entries.
2. If a matching entry is found (TLB hit), the PFN is returned immediately — typically 1–2 cycles.
3. If not found (TLB miss), the hardware page table walker reads the page table in main memory to find the mapping (~100 cycles), loads it into the TLB, and retries.
4. If the PTE has V=0 (page not in RAM), a page fault is raised to the OS.

The TLB is critical because without it, every memory access would require a page table lookup (another memory access), doubling memory access time. With ~99% TLB hit rate, translation overhead is nearly eliminated."

---

## Type 2: CALCULATE Questions

**Trigger words:** "Calculate...", "Compute...", "Find the...", "What is the CPI/AMAT/speedup..."

**What the examiner wants:**
- Correct formula
- Correct substitution
- Correct arithmetic
- Correct units

### ✅ Strategy Framework:
1. **Write the formula** (even if you could calculate in your head)
2. **Identify each variable** from the problem statement
3. **Substitute values** with labels: "Miss_Rate = 0.05"
4. **Compute step by step** — show intermediate results
5. **State the answer with units**

### Example:
> "A cache has a 4-cycle hit time and 8% miss rate. Memory access takes 100 cycles. Calculate AMAT."

```
Formula: AMAT = Hit_Time + Miss_Rate × Miss_Penalty

Given:
  Hit_Time = 4 cycles
  Miss_Rate = 0.08
  Miss_Penalty = 100 cycles

AMAT = 4 + 0.08 × 100
     = 4 + 8
     = 12 cycles
```

**Common mistakes to avoid:**
- Using hit rate instead of miss rate → wrong formula
- Forgetting to include hit time
- Not stating "cycles" as the unit

---

## Type 3: COMPARE/CONTRAST Questions

**Trigger words:** "Compare...", "What is the difference between...", "Contrast A and B...", "When would you use X vs Y?"

**What the examiner wants:**
- Multiple meaningful dimensions of comparison
- Clear pros/cons for each
- Conclusion about when each is appropriate

### ✅ Strategy Framework:
Use a **table format** when possible:

| Dimension | Option A | Option B |
|-----------|----------|----------|
| Key property 1 | ... | ... |
| Key property 2 | ... | ... |
| Advantage | ... | ... |
| Disadvantage | ... | ... |
| Best used when | ... | ... |

Then add 1–2 sentences of conclusion.

### Example:
> "Compare write-through and write-back cache policies."

| Aspect | Write-Through | Write-Back |
|--------|--------------|-----------|
| Write hit behavior | Write cache + memory | Write cache only |
| Write miss behavior | Write memory (no-allocate) | Fetch block, then write cache |
| Memory traffic | High (every write) | Low (only on eviction) |
| Consistency | Always consistent | Requires dirty bit |
| Complexity | Simple | More complex |
| Dirty bit needed? | No | Yes |

**Conclusion:** Write-back is preferred for large caches where memory traffic is a bottleneck. Write-through is simpler and preferred for small L1 caches where consistency matters most.

---

## Type 4: DRAW/DIAGRAM Questions

**Trigger words:** "Draw a diagram...", "Sketch...", "Show the timing diagram...", "Illustrate..."

**What the examiner wants:**
- Clear, labeled diagram
- All components/stages/signals identified
- Correct structure/flow

### ✅ Strategy Framework:
1. **Label everything** — every box, arrow, stage
2. **Use text in boxes** when art isn't perfect
3. **Show direction of flow** with arrows
4. **Add brief annotations** if the diagram alone is ambiguous

### For Pipeline Timing Diagrams:
```
Draw a grid: rows = instructions, columns = clock cycles
Label stages: IF, ID, EX, MEM, WB
Mark stalls as [stall] or [s] in the cell
Mark forwarding paths with arrows
```

### For Cache Address Breakdown:
```
 31           tag           index  offset  0
 ┌───────────────────────┬────────┬───────┐
 │  TAG (18 bits)        │IDX(8b) │OFF(6b)│
 └───────────────────────┴────────┴───────┘
```

---

## Type 5: TRACE/FOLLOW Questions

**Trigger words:** "Trace...", "Follow this instruction through...", "What happens when...", "Walk through..."

**What the examiner wants:**
- Step-by-step execution
- Correct order of operations
- State of the system at each step

### ✅ Strategy Framework:
1. Start from the beginning state
2. Execute one step at a time
3. Note the state change at each step
4. Continue until the end condition

### Example:
> "Trace the following instruction sequence through the pipeline, showing all stalls."

Write each instruction as a row. Mark each stage in each clock cycle. Insert stall rows where needed.

---

## Type 6: DESIGN Questions

**Trigger words:** "Design a cache that...", "How would you implement...", "Given these constraints, design..."

**What the examiner wants:**
- Application of knowledge to a new scenario
- Trade-off analysis
- Justified choices

### ✅ Strategy Framework:
1. **Identify the constraints** (size, speed, cost, etc.)
2. **State your design decisions** and what values you chose
3. **Justify each decision** — why did you choose n-way vs direct-mapped?
4. **Calculate key metrics** (AMAT, cache size, etc.)
5. **Acknowledge trade-offs** — what did you sacrifice?

---

## 📝 Writing Style Tips

### For short answers (< 5 lines):
- Get to the point immediately
- Use bullet points if listing
- Define acronyms on first use

### For long answers (> 10 lines):
- Start with a 1-sentence summary
- Use numbered steps for sequences
- End with a brief conclusion

### For calculations:
- **ALWAYS write the formula** — it's worth partial credit
- **Label substituted values** — don't just write numbers
- **Check units** — do they make sense?

---

## ⚠️ Things That Lose Points

1. **Correct answer, no work shown** — can't give partial credit
2. **Wrong formula, correct numbers** — formula is usually worth points
3. **Right concept, wrong terminology** — use the correct technical terms
4. **Vague answers** — "cache is faster" is not an answer; "cache reduces AMAT from 100 to 12 cycles" is
5. **Ignoring part of the question** — read carefully, answer all parts
