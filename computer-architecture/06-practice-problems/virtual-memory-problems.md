# 💾 Virtual Memory Practice Problems

---

## Problem 1 — Address Breakdown Basics

System: 32-bit virtual addresses, 4KB pages.

**a)** How many bits are used for the page offset?

**b)** How many bits for the VPN?

**c)** How many entries in the single-level page table?

**d)** If each PTE is 4 bytes, how large is the page table?

---

## Problem 2 — Address Translation

System: 32-bit addresses, 4KB pages, single-level page table.

The page table contains (selected entries):
| VPN | PFN | Valid |
|-----|-----|-------|
| 0 | 5 | 1 |
| 1 | 12 | 1 |
| 2 | — | 0 |
| 3 | 7 | 1 |
| 4 | 22 | 1 |

Translate each virtual address to a physical address (or indicate page fault):

**a)** VA = 0x00003A4C

**b)** VA = 0x00000FFF

**c)** VA = 0x00002000

**d)** VA = 0x00004250

---

## Problem 3 — TLB Scenarios

System: 4KB pages, TLB has 8 entries (fully associative, LRU).

TLB initially contains:
| VPN | PFN |
|-----|-----|
| 0x10 | 0x20 |
| 0x11 | 0x31 |
| 0x12 | 0x40 |

For each access, state: TLB hit or miss, and the physical address:

**a)** VA = 0x00010A30 (VPN = 0x10)

**b)** VA = 0x00012050 (VPN = 0x12)

**c)** VA = 0x00015000 (VPN = 0x15) — PTE shows V=1, PFN=0x50

**d)** VA = 0x00013000 (VPN = 0x13) — PTE shows V=0

---

## Problem 4 — Effective Memory Access Time

System specifications:
- TLB hit time = 2 cycles
- TLB hit rate = 95%
- Page table access time = 100 cycles (additional on TLB miss)
- Memory access time = 100 cycles

**a)** Calculate EMAT (Effective Memory Access Time).

**b)** If TLB hit rate improves to 99%, what is the new EMAT?

**c)** How much improvement does the better TLB provide?

---

## Problem 5 — Two-Level Page Table

System: 32-bit VA, 4KB pages, two-level page table. VA split: 10 bits L1 | 10 bits L2 | 12 bits offset.

**a)** How many entries in the L1 page directory?

**b)** How many entries in each L2 page table?

**c)** A process only uses addresses from 0x00000000 to 0x000FFFFF (1MB). How many L2 page tables are needed?

**d)** How much memory does the page table use for this process? (4 bytes per entry)

**e)** Compare to the single-level page table size for the same system.

---

## Problem 6 — Page Replacement

System: Physical memory = 3 frames. Page reference string: **1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5**

**a)** Trace through using **FIFO** replacement. Count page faults.

**b)** Trace through using **LRU** replacement. Count page faults.

**c)** Which performs better and why?

---

## Problem 7 — Full Translation with TLB

System: 16-bit virtual addresses, 512-byte pages, TLB with 4 entries (LRU), single-level page table.

Calculate:
**a)** Offset bits, VPN bits

**b)** Number of page table entries

**c)** Given VA = 0x2345, what is the VPN and offset?

**d)** If TLB has VPN 0x11 → PFN 0x8, translate VA = 0x2345 if VPN 0x11 → PFN 0x8 is in TLB. (Hint: is VPN 0x11 the right VPN for this address?)

---

## Problem 8 — Page Table Size for 64-bit System

System: 64-bit virtual addresses, 4KB pages, 4-level page table (9 bits each + 12 bits offset = 48 bits used), 8-byte PTEs.

**a)** How many VPN bits are used?

**b)** How many entries in one page table at any level?

**c)** What is the worst-case total page table memory for ONE process?

**d)** Why is a multi-level page table essential for 64-bit systems?

---

*See [SOLUTIONS.md](SOLUTIONS.md) for complete solutions.*
