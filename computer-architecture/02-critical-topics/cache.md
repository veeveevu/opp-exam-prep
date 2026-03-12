# 🗃️ Cache Memory — Complete Study Guide

> 🔴 **CRITICAL TOPIC** — This is very likely the most tested topic on your exam.

---

## 1. Core Concepts

- **Cache** is a small, fast memory that stores recently/frequently accessed data to bridge the speed gap between the CPU and main memory.
- **Locality principle** makes caches effective:
  - **Temporal locality:** If you access data now, you'll likely access it again soon (loops, repeated variables)
  - **Spatial locality:** If you access data at address X, you'll likely access nearby addresses (arrays, sequential code)
- The cache stores data in **blocks** (also called cache lines). When any byte is accessed, the whole block is loaded.
- **Memory hierarchy:** Registers → L1 Cache → L2 Cache → L3 Cache → Main Memory → Disk
- Three types of organization determine how blocks map to cache locations: **direct mapped**, **set associative**, and **fully associative**.
- Two policies control writes: **write-through** (safe, simple) and **write-back** (efficient, complex).
- **AMAT** is the key metric: `Hit Time + Miss Rate × Miss Penalty`.

---

## 2. Key Formulas & Calculations

### AMAT (Average Memory Access Time)
```
AMAT = Hit_Time + Miss_Rate × Miss_Penalty

Two-level:
AMAT = L1_Hit_Time + L1_Miss_Rate × (L2_Hit_Time + L2_Miss_Rate × Mem_Penalty)
```

### Address Bit Breakdown
```
Offset bits  = log₂(Block_Size_in_bytes)      ← selects byte within block
Index bits   = log₂(Number_of_Sets)            ← selects cache set
Tag bits     = Total_bits − Index_bits − Offset_bits  ← identifies block
```

### Cache Parameters
```
Number_of_Sets = Total_Cache_Size / (Associativity × Block_Size)
Total_Blocks   = Total_Cache_Size / Block_Size
Cache_Size     = Num_Sets × Associativity × Block_Size
```

---

## 3. Visual Diagrams

### Memory Hierarchy
```
  ┌─────────────┐
  │  Registers  │  < 1 ns    (Bytes)
  └──────┬──────┘
         ▼
  ┌─────────────┐
  │  L1 Cache   │  1-4 ns    (32-64 KB)    ← On-chip
  └──────┬──────┘
         ▼
  ┌─────────────┐
  │  L2 Cache   │  10-20 ns  (256 KB-1 MB) ← On-chip
  └──────┬──────┘
         ▼
  ┌─────────────┐
  │  L3 Cache   │  30-50 ns  (4-32 MB)     ← On-chip (shared)
  └──────┬──────┘
         ▼
  ┌─────────────┐
  │ Main Memory │  50-100 ns (GBs)          ← Off-chip (DRAM)
  └──────┬──────┘
         ▼
  ┌─────────────┐
  │    Disk     │  ms range  (TBs)
  └─────────────┘
```

### Address Breakdown (32-bit address, 64-byte blocks, 4-way set associative, 32KB cache)
```
 31                               0
 ┌──────────────┬─────────┬───────┐
 │     TAG      │  INDEX  │OFFSET │
 │   (21 bits)  │ (5 bits)│(6 bits)│
 └──────────────┴─────────┴───────┘

Offset = log₂(64) = 6 bits
Num sets = 32KB / (4 × 64B) = 128 sets → Index = log₂(128) = 7 bits
Tag = 32 - 7 - 6 = 19 bits

Wait, let's recompute:
Num sets = 32*1024 / (4 * 64) = 32768 / 256 = 128 sets → Index = 7 bits
Tag = 32 - 7 - 6 = 19 bits
```

### Direct Mapped Cache Organization
```
Cache Index │ Valid │    Tag     │ Block Data (64 bytes)
────────────┼───────┼────────────┼──────────────────────
    0x00    │   1   │ 0xA2B3...  │ [byte0][byte1]...[byte63]
    0x01    │   1   │ 0x3F12...  │ [byte0][byte1]...[byte63]
    0x02    │   0   │    ---     │ (invalid)
    ...     │  ...  │   ...      │ ...
```

### Set Associative Cache (2-way)
```
Set  │ Way 0                      │ Way 1
     │ Valid │ Tag  │ Data         │ Valid │ Tag  │ Data
─────┼───────┼──────┼─────────────┼───────┼──────┼──────────
  0  │   1   │ A3   │ [64 bytes]  │   1   │ B7   │ [64 bytes]
  1  │   1   │ C2   │ [64 bytes]  │   0   │ --   │ (empty)
  2  │   0   │ --   │ (empty)     │   0   │ --   │ (empty)
```

---

## 4. Worked Examples

### Example 1: Address Breakdown
**Problem:** 16KB direct-mapped cache, 32-byte blocks, 32-bit addresses. Break down address `0x00003A4B`.

**Solution:**
1. Offset bits = log₂(32) = **5 bits** (bits [4:0])
2. Number of blocks = 16KB / 32B = 512 blocks → Index bits = log₂(512) = **9 bits** (bits [13:5])
3. Tag bits = 32 − 9 − 5 = **18 bits** (bits [31:14])

Address `0x00003A4B` = `0000 0000 0000 0000 0011 1010 0100 1011`

- **Offset** = `0 1011` = 11 (byte 11 within block)
- **Index** = `1 0010 0010` = wait, let me redo bit by bit:
  - Binary: `0000 0000 0000 0000 0011 1010 0100 1011`
  - Bits [4:0] = `01011` → Offset = **11**
  - Bits [13:5] = `000100101` → Index = **37**
  - Bits [31:14] = `000000000000000000` → Tag = **0**

**Verify:** This address would map to cache index 37.

---

### Example 2: AMAT Calculation
**Problem:** L1 has hit time = 2 cycles, miss rate = 10%. Memory access time = 100 cycles.

```
AMAT = 2 + 0.10 × 100 = 2 + 10 = 12 cycles
```

**With L2 cache:** L2 hit time = 10 cycles, L2 miss rate = 50%.

```
AMAT = 2 + 0.10 × (10 + 0.50 × 100)
     = 2 + 0.10 × (10 + 50)
     = 2 + 0.10 × 60
     = 2 + 6
     = 8 cycles
```

Adding L2 reduced AMAT from 12 to 8 cycles — a 33% improvement.

---

### Example 3: Cache Miss Analysis
**Problem:** Loop accessing array of 1024 integers (4 bytes each), 4KB direct-mapped cache, 16-byte blocks.

- Array size = 1024 × 4 = 4096 bytes
- Cache size = 4096 bytes
- Block size = 16 bytes → 4 integers per block
- Number of blocks = 4096/16 = 256

**First pass (cold misses):** Every 4th access is a miss (loading a new block)
- Miss rate = 25% on first pass

**Second pass:** If array fits in cache, all hits → 0% miss rate
- But 4096-byte array exactly fills 4096-byte cache → may cause conflict misses in direct-mapped!

**Lesson:** Direct-mapped cache can have 100% miss rate even when data fits in cache (conflict misses).

---

## 5. Common Exam Questions

### "Explain the difference between direct-mapped and set-associative cache"
**Answer framework:**
- Direct mapped: Each block has **exactly one** possible location (index determines location)
- Set associative: Each block can go in **any of n ways** within its set (more flexible)
- Set associative has fewer conflict misses but more complex hardware (n comparators per set)
- Trade-off: associativity increases with area/power but improves hit rate

### "Calculate AMAT for the following system..."
**Step-by-step:**
1. Write the formula: `AMAT = Hit_Time + Miss_Rate × Miss_Penalty`
2. If multi-level: `AMAT = HT1 + MR1 × (HT2 + MR2 × MP)`
3. Substitute given values
4. Compute — **always include units (cycles or ns)**

### "What happens on a write miss with write-back policy?"
**Answer:** Write-allocate: fetch the block into cache (this causes the miss penalty), then write to the cache block. The dirty bit is set. Memory is NOT immediately updated.

### "Draw and label the address breakdown for a given cache configuration"
**Steps:**
1. Calculate offset bits = log₂(block size)
2. Calculate number of sets = total cache / (ways × block size)
3. Calculate index bits = log₂(number of sets)
4. Tag bits = total address bits − index − offset
5. Draw the bit field diagram with labels and bit widths

---

## 6. Tricky Points

### Write policy pairing
- Write-through is **always** paired with no-write-allocate (no point loading block you'll immediately write to memory anyway)
- Write-back is **always** paired with write-allocate

### LRU in direct-mapped caches
LRU is irrelevant in a direct-mapped cache — there's only one possible location, so you always replace whatever is there.

### Fully associative = maximum flexibility, maximum hardware cost
In a fully associative cache with N blocks, you need N comparators working in parallel to check all tags simultaneously.

### Block offset vs word offset
Be careful whether the problem uses byte addressing or word addressing. Exam problems sometimes use 32-bit words → offset = log₂(block_size / 4).

### Valid bit matters on startup
When the computer starts, all valid bits are 0 — every first access is a compulsory miss regardless of cache type.

---

## 7. Quick Self-Test

**Q1:** A cache has 64KB, 4-way set associative, 64-byte blocks, 32-bit addresses. How many index bits?

**Q2:** If AMAT = 5 cycles, hit time = 2 cycles, miss penalty = 60 cycles, what is the miss rate?

**Q3:** What type of miss occurs when a cache is accessed for the very first time with a new block?

**Q4:** Why does write-back require a dirty bit but write-through does not?

**Q5:** A direct-mapped cache has 256 sets and 32-byte blocks. What address maps to index 5, assuming byte address 0?

---

### Answers to Self-Test

**A1:** 64KB / (4 × 64B) = 256 sets → Index = log₂(256) = **8 bits**

**A2:** AMAT = Hit_Time + Miss_Rate × Miss_Penalty → 5 = 2 + MR × 60 → MR = 3/60 = **5%**

**A3:** **Cold miss** (compulsory miss) — first access to any block is always a miss.

**A4:** Write-back only writes to memory when the block is evicted. The dirty bit tracks whether the block has been written (and thus differs from memory). Write-through always keeps memory up to date, so no tracking needed.

**A5:** Block address 5 maps to index 5. Byte address = 5 × 32 = **160 (0xA0)**. But many byte addresses map to index 5 (e.g., 160, 160+256×32, etc.).
