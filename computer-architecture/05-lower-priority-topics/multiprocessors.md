# 🔲 Multiprocessors — Study Guide

> ⚪ **LOWER PRIORITY** — Read summaries only. Focus on coherence concepts.

---

## 1. Core Concepts

- **Multiprocessors** have multiple CPUs sharing resources (memory, I/O).
- **SMP (Symmetric Multiprocessor):** All CPUs share one memory with equal access time.
- **NUMA (Non-Uniform Memory Access):** Each CPU has local memory (faster) and can access other CPUs' memory (slower).
- **Cache coherence:** Multiple caches may have different copies of the same memory location — they must be kept consistent.
- **Synchronization** is required when multiple processors access shared data.

---

## 2. Cache Coherence

**Problem:**
```
CPU1 cache: Address X = 5
CPU2 cache: Address X = 5

CPU1 writes X = 10:
  CPU1 cache: X = 10
  CPU2 cache: X = 5  ← STALE! Inconsistency!
  Main memory: X = 5 (if write-back)
```

**Solution: Cache coherence protocols**

### MESI Protocol (4 states per cache line)
| State | Meaning |
|-------|---------|
| **M (Modified)** | Only this cache has it; differs from memory (dirty) |
| **E (Exclusive)** | Only this cache has it; matches memory (clean) |
| **S (Shared)** | Multiple caches may have it; matches memory |
| **I (Invalid)** | This cache line is invalid; do not use |

**Key transitions:**
- CPU reads a shared line → stays Shared
- CPU writes a shared line → broadcasts Invalidate → others go to Invalid → becomes Modified
- Another CPU reads → Modified → Shared (write back to memory first)

---

## 3. Synchronization

### Lock (Mutex)
```assembly
# Test-and-Set (atomic read-modify-write)
lock:
  LL   $t0, lock_addr    # Load Linked (atomic read)
  BNE  $t0, $zero, lock  # If lock taken, spin
  ADDI $t0, $zero, 1     # t0 = 1 (lock value)
  SC   $t0, lock_addr    # Store Conditional (succeeds only if no intervening write)
  BEQ  $t0, $zero, lock  # If SC failed (someone else wrote), retry
  # Critical section
unlock:
  SW   $zero, lock_addr  # Release lock
```

---

## 4. Amdahl's Law for Multiprocessors

```
Speedup = 1 / ((1 − P) + P/n)

where:
  P = parallelizable fraction
  n = number of processors

Max speedup = 1/(1−P)
```

This shows why even with many CPUs, the sequential part limits speedup — the fundamental scalability challenge.

---

## 5. False Sharing

**False sharing:** Two CPUs access *different* variables that happen to be in the *same cache line*. Every write by either CPU invalidates the other's cache line — causes coherence traffic even though they're not sharing data!

**Fix:** Pad data structures to align each shared variable to its own cache line.

---

## 6. Quick Self-Test

**Q1:** What is cache coherence and why is it a problem in multiprocessors?

**Q2:** What are the 4 states in the MESI protocol?

**Q3:** Using Amdahl's Law: 80% parallelizable. 8 processors. What speedup?

---

### Answers

**A1:** Cache coherence ensures all CPUs see a consistent view of memory when they each have their own cache. It's a problem because writes by one CPU are not immediately visible to other CPUs' caches, leading to stale data.

**A2:** Modified, Exclusive, Shared, Invalid.

**A3:** `Speedup = 1 / (0.2 + 0.8/8) = 1 / (0.2 + 0.1) = 1 / 0.3 ≈ 3.33×`
