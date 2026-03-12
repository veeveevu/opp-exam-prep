# 🚌 Buses & Memory — Study Guide

> 🟢 **MEDIUM PRIORITY** — Understand bus protocol and memory types; calculations may appear.

---

## 1. Core Concepts

- A **bus** is a shared communication channel connecting CPU, memory, and I/O devices.
- **Bus bandwidth** limits data transfer rate between components.
- **DRAM** (Dynamic RAM) is the main memory technology: cheap, dense, but slow (requires periodic refresh).
- **SRAM** (Static RAM) is used for caches: fast, no refresh needed, but expensive and less dense.
- **Bus protocols** control when devices can transmit (avoid collisions).
- Modern systems use **point-to-point links** (PCIe, HyperTransport) instead of shared buses.

---

## 2. Key Formulas

```
Bus Bandwidth = Bus Width (bits) × Bus Frequency (Hz) / 8  [bytes/second]

Transfer Time = (1 / Bus_Freq) × (address_cycles + data_cycles)

Effective Bandwidth = Block_Size / Transfer_Time
```

---

## 3. Memory Types Comparison

| Type | Speed | Cost | Refresh? | Volatile? | Use |
|------|-------|------|---------|---------|-----|
| **SRAM** | Very fast (1-4 ns) | High | No | Yes | Cache |
| **DRAM** | Moderate (50-100 ns) | Low | Yes (~64ms) | Yes | Main memory |
| **Flash/NAND** | ~μs (SSD) | Medium | No | No | Storage |
| **HDD** | ~ms | Very low | No | No | Mass storage |
| **ROM** | Fast read | Low | No | No | Firmware |

---

## 4. Bus Architecture

### Simple Bus (Shared)
```
CPU ──────────────────────────────────── Memory
      │         │          │
    Timer    Keyboard    Disk
    
Problems: Only one device can use bus at a time
          All devices run at same speed (bottleneck)
```

### Modern Memory Hierarchy (Point-to-Point)
```
CPU ←→ L1/L2/L3 Cache ←→ Memory Controller ←→ DDR DRAM
  │
  └──→ PCIe Bus ←→ GPU, SSD, Network Card
  │
  └──→ I/O Hub ←→ USB, SATA, Ethernet
```

---

## 5. Bus Transactions

### Memory Read (Synchronous Bus):
```
Cycle 1:  CPU asserts address on bus, asserts Read signal
Cycles 2-4: Memory sees address, fetches data
Cycle 5:  Memory puts data on bus
Cycle 6:  CPU reads data from bus
```

### Improving Bandwidth:
1. **Wider bus:** 64-bit instead of 32-bit → 2× bandwidth
2. **Higher frequency:** 400MHz instead of 200MHz → 2× bandwidth  
3. **Block transfers:** Send multiple words per transaction (amortize address overhead)
4. **Split-transaction bus:** Bus released while memory fetches data; other transactions can proceed

---

## 6. Quick Self-Test

**Q1:** A bus is 64 bits wide, running at 200 MHz. What is the maximum bandwidth?

**Q2:** Why is SRAM used for caches rather than DRAM?

**Q3:** What is the advantage of a split-transaction bus over a traditional synchronous bus?

---

### Answers

**A1:** 64 bits × 200×10⁶ Hz = 12.8 × 10⁹ bits/s = **1.6 GB/s**

**A2:** SRAM is much faster (no refresh needed, access in 1-4 ns vs 50-100 ns for DRAM). The higher cost is acceptable for the small cache sizes (KB-MB range).

**A3:** A split-transaction bus releases the bus between sending a request and receiving the response. Other devices can use the bus during the memory latency, greatly improving bus utilization.
