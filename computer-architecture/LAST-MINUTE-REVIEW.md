# ⚡ LAST-MINUTE REVIEW — 1 Hour Before the Exam

> Read this the morning of your exam. Nothing new — just reinforcement.

---

## 🔢 TOP 10 FORMULAS

| # | Formula | Variables |
|---|---------|-----------|
| 1 | **AMAT = Hit Time + Miss Rate × Miss Penalty** | HT = cache hit time (cycles), MR = fraction missed, MP = time to fetch from next level |
| 2 | **CPU Time = IC × CPI × Clock Cycle Time** | IC = instruction count, CPI = avg cycles/instruction, CCT = 1/clock_freq |
| 3 | **Speedup = Time_old / Time_new** | Always > 1 if improved |
| 4 | **Amdahl's Law: S = 1 / ((1−P) + P/s)** | P = parallelizable fraction, s = speedup of that part |
| 5 | **CPI_effective = Σ(CPI_i × freq_i)** | Weighted average across instruction types |
| 6 | **Direct-mapped index = (block_addr) mod (num_blocks)** | Block address = byte address / block size |
| 7 | **MIPS = (IC / execution_time) / 10⁶** | Misleading metric — do NOT use to compare different ISAs |
| 8 | **Pipeline Speedup ≈ n** (ideal, n stages) | Actual: n / (1 + stall_fraction × n) |
| 9 | **VPN bits = VA_bits − page_offset_bits** | Page offset bits = log₂(page_size) |
| 10 | **Number of page table entries = 2^(VPN bits)** | Each entry = PTE |

---

## 📖 TOP 15 DEFINITIONS

1. **Cache Hit** — Requested data found in cache; served quickly
2. **Cache Miss** — Data not in cache; must fetch from lower memory level
3. **AMAT** — Average Memory Access Time; weighted average across hit/miss
4. **Write-Through** — Write to cache AND memory simultaneously
5. **Write-Back** — Write to cache only; write to memory when evicted (uses dirty bit)
6. **LRU** — Least Recently Used replacement policy
7. **TLB** — Translation Lookaside Buffer; cache for virtual→physical address mappings
8. **Page Fault** — Requested page not in physical memory; OS loads it from disk
9. **RAW Hazard** — Read After Write; an instruction reads before a previous write finishes
10. **Forwarding/Bypassing** — Route result directly from EX/MEM stage to earlier stage (avoids stall)
11. **Branch Prediction** — Guess branch outcome before resolution to keep pipeline full
12. **Superscalar** — CPU that issues multiple instructions per clock cycle
13. **Virtual Address** — Address used by program (before translation)
14. **Physical Address** — Actual hardware address (after translation)
15. **Dirty Bit** — Marks a cache line as modified (write-back caches)

---

## 📊 CRITICAL COMPARISON TABLES

### Cache Organization Types
| Type | Mapping | Index Bits | Conflict Misses | HW Cost |
|------|---------|------------|----------------|---------|
| Direct Mapped | 1 block per set | log₂(num_sets) | High | Low |
| Set Associative (n-way) | n blocks per set | log₂(num_sets) | Medium | Medium |
| Fully Associative | Any block, any set | 0 bits | None | High |

### Write Policies
| Policy | Write Hit | Write Miss | Memory Traffic | Complexity |
|--------|-----------|------------|----------------|------------|
| Write-Through + No-Write-Allocate | Write cache + mem | Write mem only | High | Simple |
| Write-Back + Write-Allocate | Write cache only | Fetch to cache, then write | Low | Complex |

### Pipeline Hazards
| Hazard | Type | Example | Solution |
|--------|------|---------|----------|
| RAW | Data | `ADD r1; SUB r2, r1` | Forwarding or stall |
| WAR | Data (rare in-order) | `SUB r1; ADD r1` | Rename registers |
| WAW | Data (rare in-order) | `ADD r1; MUL r1` | Stall or rename |
| Control | Branch | `BEQ ...` | Prediction, delay slot |
| Structural | Resource | Two instructions need MEM | Stall or duplicate HW |

### TLB vs Page Table
| | TLB | Page Table |
|--|-----|-----------|
| Location | CPU chip (fast) | Main memory (slow) |
| Stores | Recent VA→PA mappings | All VA→PA mappings |
| Size | Small (32–1024 entries) | Large (entire VA space) |
| Miss action | Walk page table | Page fault if not found |

---

## 🔄 WORKED EXAMPLES — One Per Critical Topic

### 1. Cache Address Breakdown
**Problem:** 16KB direct-mapped cache, 64-byte blocks, 32-bit addresses. Find tag/index/offset for address 0x12345678.

- Offset bits = log₂(64) = **6 bits**
- Index bits = log₂(16KB / 64) = log₂(256) = **8 bits**
- Tag bits = 32 − 8 − 6 = **18 bits**

Address 0x12345678 = `0001 0010 0011 0100 0101 0110 0111 1000`
- **Offset** = `111000` = 0x38
- **Index** = `01010110` = 0x56
- **Tag** = `000100100011010001` = 0x048D1

### 2. Pipeline Timing Diagram (5 instructions, no hazards)
```
       CC1  CC2  CC3  CC4  CC5  CC6  CC7  CC8  CC9
I1:    IF   ID   EX   MEM  WB
I2:         IF   ID   EX   MEM  WB
I3:              IF   ID   EX   MEM  WB
I4:                   IF   ID   EX   MEM  WB
I5:                        IF   ID   EX   MEM  WB
```
5 instructions complete in 9 cycles. Ideal CPI = 1.

### 3. AMAT Calculation
**L1:** Hit time = 4 cycles, Miss rate = 5%
**L2:** Hit time = 20 cycles, Miss rate = 40% (of L1 misses)
**Mem:** Penalty = 200 cycles

`AMAT = 4 + 0.05 × (20 + 0.40 × 200) = 4 + 0.05 × (20 + 80) = 4 + 5 = 9 cycles`

### 4. Amdahl's Law
**Problem:** 40% of program is parallelizable. You use 4 processors. What speedup?

`S = 1 / ((1 − 0.4) + 0.4/4) = 1 / (0.6 + 0.1) = 1 / 0.7 ≈ 1.43×`

### 5. Virtual Address Translation
**32-bit VA, 4KB pages, single-level page table**

- Page offset = log₂(4096) = 12 bits
- VPN = 32 − 12 = 20 bits → page table has 2²⁰ = 1M entries

VA = 0x00405004:
- VPN = 0x00405 → look up PT[0x00405] = PFN (say 0x00200)
- Offset = 0x004
- PA = (0x00200 << 12) | 0x004 = 0x00200004

---

## 🎯 EXAM QUESTION TEMPLATES

### "Explain how X works"
→ **What it is** (1 sentence) + **How it works step-by-step** (3–5 steps) + **Why it matters** (1–2 sentences)

### "Compare A vs B"
→ Use a **table**: columns = A, B; rows = key dimensions. Add 1–2 sentences of conclusion.

### "Calculate the following"
→ **Write the formula first**, substitute values with labels, solve step-by-step, **state the unit**.

### "Draw a diagram showing..."
→ Draw clearly, label every component/signal/stage, add brief annotations.

### "What happens when..."
→ Describe the sequence of events in order. Be specific about which hardware component acts.

---

## 🧠 LAST 5 THINGS TO REMEMBER

1. **Always show your work** — even a wrong formula with correct substitution gets partial credit
2. **Write units** — cycles, bytes, seconds — always specify
3. **Draw diagrams** — even rough ASCII-style helps examiners see your thinking
4. **Define acronyms** — write out TLB, AMAT, CPI, etc. once when first used
5. **Read the question twice** — notice whether they ask "calculate," "explain," or "compare"

---

> 🍀 **Good luck! You've prepared well. Trust the work you put in tonight.**
> 
> 💪 **Remember: partial credit is always available — show your reasoning on every question!**
