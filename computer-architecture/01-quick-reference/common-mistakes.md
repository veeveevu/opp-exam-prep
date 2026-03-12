# ⚠️ Common Mistakes — Pitfalls to Avoid

> These are the errors students make most often. Read this the night before and the morning of the exam.

---

## 🗃️ Cache Mistakes

### ❌ Forgetting to convert bytes to blocks before computing the index
**Wrong:** Using byte address directly for index
**Right:** `Block Address = Byte Address / Block Size`, then `Index = Block Address mod Num_Sets`

### ❌ Mixing up block size and cache size
- **Block size** = size of one cache line (e.g., 64 bytes)
- **Cache size** = total size (e.g., 32 KB)
- Number of blocks = Cache Size / Block Size

### ❌ Wrong order of address bit breakdown
- From **lowest** bits to highest: **offset** → **index** → **tag**
- Offset bits = log₂(block size)
- Index bits = log₂(number of sets)
- Tag bits = everything left over

### ❌ Using AMAT formula with hit rate instead of miss rate
**Wrong:** `AMAT = Hit Time × Hit Rate + Miss Penalty`
**Right:** `AMAT = Hit Time + Miss Rate × Miss Penalty`

### ❌ Confusing write-through and write-back
- **Write-through:** Every write goes to memory immediately — always consistent, more traffic
- **Write-back:** Only writes to cache; updates memory when evicted — needs dirty bit

### ❌ Saying fully associative has no misses
Fully associative eliminates **conflict** misses only. Cold and capacity misses still occur.

---

## ⚙️ Pipelining Mistakes

### ❌ Drawing pipeline diagrams with wrong number of cycles
- `n` instructions in a `k`-stage pipeline take `k + (n-1)` cycles (no stalls)
- Example: 5 instructions, 5-stage pipeline = 5 + 4 = 9 cycles

### ❌ Saying forwarding eliminates ALL data hazards
- Forwarding resolves **most** RAW hazards
- **Load-use hazard** still requires a 1-cycle stall even with forwarding
  (Because MEM doesn't complete until after the next instruction needs the value)

### ❌ Confusing hazard types
- **Data hazard** = dependency between instruction values
- **Control hazard** = branch/jump changes PC unexpectedly
- **Structural hazard** = two instructions need the same hardware resource

### ❌ Forgetting that branches cause control hazards
Every branch is a potential control hazard. The CPU doesn't know the next PC until EX or MEM stage.

### ❌ Calculating CPI incorrectly
`CPI_effective = 1 + stall_cycles_per_instruction`
Don't forget to include stalls from both branches AND load-use hazards.

---

## 💾 Virtual Memory Mistakes

### ❌ Confusing VPN and VPN bits
- VPN = the actual virtual page number value
- VPN bits = number of bits used for VPN = VA bits − offset bits

### ❌ Adding page table entries for physical frames
Page tables map **virtual** space to physical. The table has `2^(VPN bits)` entries, regardless of physical memory size.

### ❌ Forgetting the TLB lookup comes before page table
Process: Check TLB first → if miss → walk page table → if page not in RAM → page fault

### ❌ Saying a TLB miss = page fault
- TLB miss: mapping not cached in TLB → walk page table (page may still be in RAM)
- Page fault: page is not in physical memory at all → OS loads from disk

### ❌ Page offset bits wrong
`Page offset bits = log₂(page size in bytes)`
For 4KB pages: log₂(4096) = 12 offset bits ✓

---

## ⚡ Performance Mistakes

### ❌ Using MIPS to compare processors with different ISAs
MIPS is instruction-count-dependent. A RISC CPU doing 1000 MIPS might be slower than a CISC CPU at 200 MIPS if the RISC needs many more instructions.

### ❌ Misapplying Amdahl's Law
`Speedup = 1 / ((1-P) + P/S)`
- P is the **fraction improved**, not the fraction unimproved
- The maximum speedup (S→∞) is `1 / (1-P)` — this is your ceiling

### ❌ Confusing IC, CPI, and clock rate
- Lowering CPI does NOT always mean fewer instructions (IC is separate)
- A faster clock rate does NOT mean lower CPI
- They are independent variables

### ❌ Forgetting units in performance calculations
Always write units: cycles, seconds, ns, instructions/second. Examiners check.

### ❌ Not differentiating between latency and throughput
- **Latency:** Time for ONE task (important for real-time)
- **Throughput:** Rate of completing MANY tasks (important for servers)
- Pipelining improves throughput, not single-instruction latency

---

## 🔌 Interrupt / I/O Mistakes

### ❌ Confusing interrupt vs exception
- **Interrupt:** External event (keyboard, timer) — asynchronous
- **Exception:** Caused by instruction itself (divide by zero, page fault) — synchronous

### ❌ Forgetting to save/restore registers in interrupt handler
The handler must save all registers it uses (context save) and restore them before returning.

---

## 🧮 General Calculation Mistakes

### ❌ Not showing units
Always label: cycles, bytes, ns, seconds — examiners need to see you understand what you're computing.

### ❌ Not reading the question carefully
- "Calculate" → show all work, give numeric answer
- "Explain" → words, not numbers
- "Compare" → table or two-column comparison
- "Draw" → actual diagram with labels

### ❌ Rounding too early
Keep intermediate values in exact form until the final step to avoid accumulated rounding error.

### ❌ Skipping steps
Even if you know the answer, write the formula, substitute values, then solve. Partial credit is earned on steps, not just answers.
