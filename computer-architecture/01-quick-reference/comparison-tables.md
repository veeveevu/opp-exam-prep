# 📊 Comparison Tables — Quick Reference

> These tables are gold for "compare/contrast" exam questions. Study the patterns.

---

## 1. Cache Organization Types

| Feature | Direct Mapped | Set Associative (n-way) | Fully Associative |
|---------|--------------|------------------------|-------------------|
| **Mapping** | 1 block per cache slot | n blocks per set | Any block anywhere |
| **Index bits** | log₂(num_blocks) | log₂(num_sets) | 0 (no index) |
| **Tag bits** | More (larger tag) | Medium | Maximum (all non-offset bits) |
| **Conflict misses** | High | Medium | None (only capacity/cold) |
| **Hardware cost** | Cheap | Medium | Expensive (comparators for all) |
| **Hit time** | Fastest | Moderate | Slowest |
| **Typical use** | L1 (speed critical) | L2/L3 (balance) | TLB, small buffers |
| **Replacement needed?** | No (only 1 location) | Yes (within set) | Yes (any line) |

---

## 2. Write Policies

| Policy | Write Hit Behavior | Write Miss Behavior | Memory Traffic | Dirty Bit? | Use Case |
|--------|-------------------|---------------------|----------------|------------|---------|
| **Write-Through** | Write cache + memory | Write memory only (no-allocate) | High | No | Simple, small caches |
| **Write-Back** | Write cache only | Load into cache, then write (write-allocate) | Low | Yes | Large L2/L3 caches |

**Commonly paired:**
- Write-Through + No-Write-Allocate
- Write-Back + Write-Allocate

---

## 3. Cache Miss Types (The 3 Cs)

| Miss Type | Cause | Reducible By |
|-----------|-------|-------------|
| **Cold (Compulsory)** | First access to a block — unavoidable | Larger blocks, prefetching |
| **Capacity** | Cache too small for working set | Larger cache |
| **Conflict** | Too many blocks map to same set | Higher associativity |

---

## 4. Replacement Policies

| Policy | How It Works | Advantage | Disadvantage |
|--------|-------------|-----------|-------------|
| **LRU** | Evict least recently used block | Best approximates optimal | Complex hardware tracking |
| **FIFO** | Evict oldest-loaded block | Simple | Ignores access pattern (Bélády's anomaly) |
| **Random** | Evict random block | Simple, predictable worst-case | Not adaptive |
| **Optimal (Bélády's)** | Evict block used furthest in future | Best possible | Requires future knowledge (theoretical only) |

---

## 5. RISC vs CISC

| Feature | RISC | CISC |
|---------|------|------|
| **Instructions** | Few, simple, uniform | Many, complex, variable |
| **Instruction size** | Fixed (e.g., 32 bits) | Variable |
| **Memory access** | Load/Store only | Many instructions access memory |
| **Registers** | Many (e.g., 32) | Fewer |
| **Clock cycles/instruction** | ~1 (usually) | Varies (1–many) |
| **Compiler** | Does more work | Hardware does more work |
| **Examples** | MIPS, ARM, RISC-V | x86, x86-64 |
| **Pipeline friendliness** | High (fixed format) | Lower (decode complexity) |

---

## 6. Pipeline Hazard Types

| Hazard | Category | Cause | Solutions |
|--------|----------|-------|-----------|
| **RAW** | Data | Instruction reads result before it's written | Forwarding, stall (bubble) |
| **WAR** | Data | Later instruction writes before earlier reads | Register renaming |
| **WAW** | Data | Two writes to same register out of order | Stall, register renaming |
| **Control** | Branch/Jump | PC changes unexpectedly | Branch prediction, flush, delay slot |
| **Structural** | Resource | Two instructions need same HW | Stall, duplicate hardware |

---

## 7. Branch Prediction Types

| Type | Basis | Examples | Accuracy |
|------|-------|---------|---------|
| **Static: Always Taken** | Assumption | Simple predictor | ~60-70% for loops |
| **Static: Always Not Taken** | Assumption | Simple predictor | ~60% typical |
| **Static: BTFN** | Direction (backward=taken, forward=not taken) | BTFN | Better for loops |
| **Dynamic: 1-bit** | Last outcome | 1-bit counter | Good but thrashes |
| **Dynamic: 2-bit** | Recent history | 2-bit saturating counter | Better on loop exit |
| **Dynamic: Correlating** | Global branch history | (m,n) predictor | High accuracy |
| **Dynamic: Tournament** | Meta-predictor chooses | Modern CPUs | Very high accuracy |

---

## 8. Virtual Memory: Single vs Multi-Level Page Table

| Feature | Single-Level | Two-Level | Inverted |
|---------|-------------|----------|---------|
| **Structure** | Flat array | Tree of tables | One entry per physical frame |
| **Size** | 2^VPN × PTE_size (always full) | Smaller in sparse cases | Proportional to physical memory |
| **Lookup speed** | 1 memory access | 2 memory accesses | Slow (search needed) |
| **Sparse address space** | Wastes space | Efficient | Very efficient |
| **Example** | Simple 32-bit systems | x86-32 (2-level) | PowerPC |

---

## 9. I/O Methods

| Method | CPU Involvement | How It Works | Efficiency |
|--------|----------------|-------------|-----------|
| **Polling (Busy Wait)** | 100% during I/O | CPU loops checking status | Low (wastes cycles) |
| **Interrupt-Driven** | Only during transfer | Device interrupts when ready | Better |
| **DMA** | Minimal | Device transfers directly to memory | Highest |

---

## 10. Memory Hierarchy Comparison

| Level | Size | Speed | Cost/bit | Technology |
|-------|------|-------|----------|-----------|
| **Registers** | Bytes | < 1 ns | Highest | Flip-flops |
| **L1 Cache** | KB (32–64 KB) | 1–4 cycles | Very high | SRAM |
| **L2 Cache** | KB–MB (256K–1M) | 10–20 cycles | High | SRAM |
| **L3 Cache** | MB (4–32 MB) | 30–50 cycles | Medium | SRAM |
| **Main Memory** | GB | 100–300 cycles | Low | DRAM |
| **SSD** | TB | 50,000+ cycles | Very low | Flash |
| **HDD** | TB | millions of cycles | Lowest | Magnetic |

---

## 11. Superscalar vs In-Order Pipeline

| Feature | In-Order Pipeline | Superscalar (Out-of-Order) |
|---------|-------------------|---------------------------|
| **Instructions issued/cycle** | 1 | Multiple (2, 4, 8+) |
| **Execution order** | Program order | Any order with no dependency |
| **Hardware complexity** | Low | High (reorder buffer, etc.) |
| **IPC** | ≤ 1 | > 1 (potentially) |
| **Examples** | Simple MIPS | Intel Core, AMD Zen |

---

## 12. Virtualization Levels

| Feature | OS Virtualization | Full Virtualization | Paravirtualization |
|---------|-----------------|--------------------|--------------------|
| **Guest OS modified?** | Yes | No | Yes |
| **Performance** | High | Moderate | High |
| **Isolation** | Process-level | Full | Full |
| **Requires HW support?** | No | Sometimes | No |
