# 🧮 Cache Practice Problems

> Work through these on **paper** — this is a paper exam! Show all work.

---

## Problem 1 — Address Breakdown (Basic)

A **direct-mapped** cache has:
- Cache size: 8KB
- Block size: 32 bytes
- Address space: 32 bits

**a)** How many blocks does the cache have?

**b)** How many bits are used for the offset, index, and tag?

**c)** Show the bit breakdown for address `0x00001A40`.

---

## Problem 2 — Set Associative Address Breakdown

A **4-way set-associative** cache has:
- Cache size: 64KB
- Block size: 64 bytes
- Address space: 32 bits

**a)** How many sets does the cache have?

**b)** How many bits for offset, index, and tag?

**c)** What set does address `0x0000F480` map to?

---

## Problem 3 — AMAT Calculation (Single Level)

A system has:
- L1 cache: 4-cycle hit time, 8% miss rate
- Memory: 120-cycle access time (penalty on L1 miss)

**a)** Calculate AMAT.

**b)** A program runs 10⁸ instructions. Each instruction accesses memory once. How many total cycles are spent on memory access?

---

## Problem 4 — AMAT Calculation (Two Level)

A system has:
- L1: 2-cycle hit time, 5% miss rate
- L2: 12-cycle hit time, 30% miss rate (for L1 misses)
- Main memory: 200-cycle penalty

**a)** Calculate AMAT.

**b)** Compare to a single-level system with L1 only (same L1 specs, 200-cycle penalty for all L1 misses).

---

## Problem 5 — Cache Hit/Miss Trace

A **direct-mapped** cache has:
- 4 blocks (indexed 0–3)
- Block size: 1 word (for simplicity)
- Initially empty

Access sequence (block addresses): **0, 4, 8, 0, 4, 1, 4, 8, 0, 4**

For each access: state whether it's a Hit or Miss, and which block is evicted (if any).

---

## Problem 6 — Write Policies

**a)** For each access below, describe what happens in a **write-back, write-allocate** cache.

The cache is initially empty. Cache has 4 blocks, direct-mapped.

1. Read address 0
2. Read address 1
3. Write address 0 (value = 42)
4. Read address 0
5. Evict block 0 (another access forces eviction)

**b)** For the same sequence, use **write-through, no-write-allocate** policy. How does the memory traffic differ?

---

## Problem 7 — Fully Associative vs Direct Mapped

Two caches, same total size (4 blocks), LRU replacement:

Access sequence: **1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5**

**a)** Count misses with **direct-mapped** cache (index = block_addr mod 4).

**b)** Count misses with **fully associative** (LRU) cache.

**c)** Explain the difference.

---

## Problem 8 — Performance Impact of Cache

A processor runs at 2GHz. Without cache:
- CPI (just execution) = 1
- Every instruction accesses memory once
- Memory access = 100 cycles

With a cache:
- Hit time = 2 cycles
- Miss rate = 5%
- Miss penalty = 100 cycles

**a)** Calculate the CPI without cache.

**b)** Calculate the CPI with cache (using AMAT).

**c)** What is the speedup from adding the cache?

---

## Problem 9 — Cache Size and Miss Rate (Reasoning)

**For each change below, state whether miss rate increases, decreases, or stays the same, and explain why:**

**a)** Doubling the cache size (same block size, same associativity)

**b)** Doubling the block size (same total cache size)

**c)** Increasing associativity from direct-mapped to 2-way (same total size)

**d)** Switching from LRU to random replacement (same cache size and associativity)

---

## Problem 10 — Full Cache Design Problem

Design a cache for the following system:
- 32-bit byte-addressable memory
- 16KB cache
- 8-way set associative
- 64-byte blocks

**a)** Calculate number of sets.

**b)** Calculate offset, index, and tag bits.

**c)** How many bits of storage does the cache require total? (Include data, tag, valid, and dirty bits — assume write-back)

**d)** If the hit time is 3 cycles and miss rate is 3%, and memory access takes 200 cycles, what is the AMAT?

---

*See [SOLUTIONS.md](SOLUTIONS.md) for complete solutions.*
