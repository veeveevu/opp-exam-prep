# 💾 Virtual Memory — Complete Study Guide

> 🔴 **CRITICAL TOPIC** — Address translation, TLB, and page faults are almost always tested.

---

## 1. Core Concepts

- **Virtual memory** gives each program the illusion of having its own large, private address space, regardless of physical memory size.
- The OS maintains a **page table** mapping virtual pages to physical frames.
- **Translation:** Virtual Address → (page table lookup) → Physical Address
- **Pages** are fixed-size blocks (typically 4KB). The offset within a page is the same in both virtual and physical addresses.
- The **TLB** (Translation Lookaside Buffer) is a hardware cache storing recent VA→PA translations to avoid page table walks on every access.
- A **page fault** occurs when the requested page is not in physical memory — the OS loads it from disk.
- Virtual memory provides **isolation** (each process has its own address space) and **protection** (permissions per page).

---

## 2. Key Formulas & Calculations

### Address Breakdown
```
Page offset bits = log₂(page size in bytes)
VPN bits         = VA bits − page offset bits
Number of PTEs   = 2^(VPN bits)

Physical Address = PFN × page_size + page_offset
               = (PFN << offset_bits) | offset
```

### Page Table Size
```
Page Table Size = 2^(VPN_bits) × PTE_size_bytes
```

### Multi-Level Address Breakdown
```
Total VPN bits split across levels:
  L1_index bits + L2_index bits + ... = VPN_bits

Each level's table has 2^(level_index_bits) entries
```

### Effective Memory Access Time with TLB
```
EMAT = TLB_hit_rate × (TLB_time + Mem_time)
     + TLB_miss_rate × (TLB_time + Page_table_access_time + Mem_time)

Simplified:
EMAT = TLB_time + Mem_time + TLB_miss_rate × Page_table_access_time
```

---

## 3. Visual Diagrams

### Virtual to Physical Address Translation
```
Virtual Address (32-bit, 4KB pages):
 31                12  11           0
 ┌──────────────────┬──────────────┐
 │  VPN (20 bits)   │ Offset(12 b) │
 └──────────────────┴──────────────┘
         │                    │
         ▼                    │
   Page Table                 │
   ┌─────────┐                │
   │ PTE[VPN]│ = PFN + bits   │
   └────┬────┘                │
        │                     │
        ▼                     ▼
Physical Address:
 ┌──────────────────┬──────────────┐
 │  PFN (20 bits)   │ Offset(12 b) │
 └──────────────────┴──────────────┘
```

### TLB Lookup Flow
```
CPU generates VA
       │
       ▼
  Check TLB ─────── Hit ──────► PA available → access memory
       │
      Miss
       │
       ▼
  Walk Page Table
       │
       ├─ Page in memory? → Load PTE into TLB → retry → PA
       │
       └─ Page NOT in memory? → PAGE FAULT → OS loads from disk
```

### Page Table Entry (PTE) Structure
```
 ┌──────────────┬───┬───┬───┬───┬───┐
 │ PFN (20 bits)│ D │ A │ U │ W │ V │
 └──────────────┴───┴───┴───┴───┴───┘
   V = Valid bit (1 = page in memory)
   W = Write permission
   U = User/supervisor mode
   A = Accessed bit
   D = Dirty bit (page has been written)
```

### Two-Level Page Table (x86-32)
```
Virtual Address (32-bit):
 31        22  21        12  11           0
 ┌──────────┬──────────────┬──────────────┐
 │ L1 index │  L2 index    │    Offset    │
 │ (10 bits)│  (10 bits)   │   (12 bits)  │
 └──────────┴──────────────┴──────────────┘
      │           │
      ▼           ▼
 L1 Page Dir   L2 Page Table   →   PTE → PFN + Offset = PA
```

---

## 4. Worked Examples

### Example 1: Address Translation (Single Level)
**Setup:** 32-bit VA, 4KB pages, single-level page table

Given address: `0x00403A4C`

**Step 1:** Find page parameters
- Page size = 4KB = 4096 bytes → Offset bits = log₂(4096) = **12 bits**
- VPN bits = 32 − 12 = **20 bits**

**Step 2:** Break down the address
- Address in binary: `0000 0000 0100 0000 0011 1010 0100 1100`
- Offset (bits [11:0]): `1010 0100 1100` = 0xA4C = **2636**
- VPN (bits [31:12]): `0000 0000 0100 0000 0011` = **0x00403**

**Step 3:** Look up page table
- PT[0x00403] = PFN 0x00200 (assume given) + valid bit = 1

**Step 4:** Compute physical address
- PA = (0x00200 << 12) | 0xA4C = `0x00200000 | 0xA4C` = **0x00200A4C**

---

### Example 2: TLB Hit/Miss Scenario
**System:** 4KB pages, TLB has 4 entries (fully associative, LRU)

```
Initial TLB: empty

Access #1: VA = 0x1000A  VPN = 0x1  → TLB miss → load PTE → TLB: {1→PFN_A}
Access #2: VA = 0x1004B  VPN = 0x1  → TLB HIT  → fast translation
Access #3: VA = 0x2000C  VPN = 0x2  → TLB miss → load PTE → TLB: {1→A, 2→B}
Access #4: VA = 0x3000D  VPN = 0x3  → TLB miss → TLB: {1, 2, 3}
Access #5: VA = 0x4000E  VPN = 0x4  → TLB miss → TLB: {1, 2, 3, 4}
Access #6: VA = 0x5000F  VPN = 0x5  → TLB miss → evict LRU (entry 1) → TLB: {2, 3, 4, 5}
Access #7: VA = 0x1001A  VPN = 0x1  → TLB miss (evicted!) → must reload
```

**Lesson:** TLB thrashing occurs when working set exceeds TLB capacity.

---

### Example 3: Page Fault Handling
**Scenario:** Program accesses VA = 0x5000. VPN = 5. Page table shows V=0 (page not present).

**What happens:**
1. CPU generates VA = 0x5000
2. TLB miss → walk page table
3. Page table entry[5]: **Valid = 0** → **PAGE FAULT triggered**
4. OS page fault handler runs:
   - Find a free physical frame (or evict one using replacement policy, e.g., LRU)
   - Load the page from disk into that frame
   - Update PTE: set PFN = new frame, Valid = 1
   - Update TLB with new mapping
5. Restart the faulting instruction

**Cost:** A page fault can take **millions of cycles** (disk access = ~5ms, CPU ~2GHz → ~10M cycles).

---

### Example 4: Two-Level Page Table Bit Breakdown
**Setup:** 32-bit VA, 4KB pages, 4-byte PTEs, two-level page table (10+10+12)

- L1 table: 2¹⁰ = **1024 entries** × 4 bytes = 4KB (one page — nice!)
- Each L2 table: 2¹⁰ = **1024 entries** × 4 bytes = 4KB
- Total L2 tables: at most 2¹⁰ = 1024 (one per L1 entry, but only allocated for mapped regions)

**Advantage:** If process only uses 1MB of VA space, only a handful of L2 tables exist → much less memory than full single-level table (4MB).

---

## 5. Common Exam Questions

### "Explain address translation step by step"
1. CPU generates virtual address (VA)
2. Split VA into VPN and page offset
3. Check TLB for VPN → PA mapping
   - **Hit:** Get PFN, combine with offset → PA → access memory
   - **Miss:** Walk page table
4. Page table lookup using VPN
   - **Valid = 1:** Get PFN → load into TLB → combine with offset → PA
   - **Valid = 0:** **PAGE FAULT** → OS handles
5. Access physical memory at PA

### "What is the TLB and why is it important?"
**Answer:** The TLB is a small, fully associative hardware cache that stores recent virtual-to-physical address translations. Without it, every memory access would require first reading the page table (another memory access) — effectively doubling memory access time. With a TLB hit rate >99%, virtual memory overhead becomes negligible.

### "What happens on a TLB miss?"
- Hardware "page table walker" reads the page table in memory
- If the PTE is valid, load it into TLB, retry the access
- If PTE is invalid (V=0), raise a page fault exception
- OS page fault handler runs, may load page from disk

### "Compare single-level and two-level page tables"
| | Single-Level | Two-Level |
|--|-------------|----------|
| Size | Always full (2^VPN × PTE) | Only allocated entries |
| Memory access per translation | 1 | 2 |
| Efficiency for sparse spaces | Wastes memory | Efficient |

---

## 6. Tricky Points

### TLB miss ≠ page fault
- **TLB miss:** Translation not cached in TLB. Walk the page table. If found there → no page fault.
- **Page fault:** Page is NOT in physical memory (V=0 in PTE).

### Context switch and TLB
On a context switch (switching processes), the TLB must be **flushed** (or use Address Space Identifiers, ASIDs). Otherwise, process B might use process A's cached translations!

### Page size tradeoffs
- **Larger pages:** Fewer page table entries, less overhead, better TLB coverage — but more internal fragmentation
- **Smaller pages:** Less fragmentation, finer protection granularity — but larger page tables

### Page table for 64-bit systems
With 64-bit VA: 2^52 PTE × 8 bytes = 32 petabytes for a single-level table! → Multi-level (4–5 levels) is essential.

### Shared pages
Multiple processes can share physical pages by having their page tables point to the same physical frame. Used for shared libraries, copy-on-write.

---

## 7. Quick Self-Test

**Q1:** A system has 32-bit virtual addresses and 8KB pages. How many bits are used for the page offset? How many VPN bits?

**Q2:** What is the difference between a TLB miss and a page fault?

**Q3:** Why does a two-level page table use less memory than a single-level page table for a sparse address space?

**Q4:** A process accesses VA = 0x00812345. Page size = 4KB. What is the VPN and what is the offset?

**Q5:** If a TLB has 99% hit rate, TLB access = 1 cycle, memory access = 100 cycles, what is the EMAT?

---

### Answers to Self-Test

**A1:** 8KB = 8192 bytes → log₂(8192) = **13 offset bits**. VPN bits = 32 − 13 = **19 bits**. Page table has 2¹⁹ = 524,288 entries.

**A2:** 
- **TLB miss:** Translation not in TLB → walk page table → usually found there (fast fix, ~100 cycles penalty)
- **Page fault:** Page not in physical memory → OS must load from disk (~millions of cycles)

**A3:** A single-level table always allocates all 2^VPN entries. A two-level table only allocates second-level tables for regions of VA space actually used — sparse programs use much less memory.

**A4:** 0x00812345:
- Offset bits = 12, so offset = 0x345 = **837**
- VPN = 0x00812345 >> 12 = **0x00812**

**A5:** EMAT = 0.99 × (1+100) + 0.01 × (1+100+100) = 0.99 × 101 + 0.01 × 201 = 99.99 + 2.01 = **102 cycles**
(Or simplified: ≈ 1 + 100 + 0.01 × 100 = 102 cycles)
