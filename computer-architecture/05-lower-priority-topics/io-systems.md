# 🔌 I/O Systems — Study Guide

> ⚪ **LOWER PRIORITY** — Understand polling vs interrupts vs DMA; basic bus concepts.

---

## 1. Core Concepts

- **I/O devices** connect to the CPU via controllers and buses.
- Three main I/O methods: **Polling**, **Interrupt-driven**, **DMA**.
- Each device has **registers** (data, status, control) that the CPU reads/writes.
- **Memory-mapped I/O:** Device registers appear in the CPU's address space; accessed with normal load/store instructions.
- **Port-mapped I/O (Isolated I/O):** Separate address space for I/O; requires special IN/OUT instructions (x86).

---

## 2. I/O Methods Comparison

| Method | CPU Usage | Latency | Complexity | Best For |
|--------|----------|---------|-----------|---------|
| **Polling** | 100% during wait | Low | Simple | Fast devices, real-time |
| **Interrupt-driven** | Only at transfer | Medium | Moderate | Slow/unpredictable devices |
| **DMA** | Minimal (start/end) | Low | Complex | Large, fast transfers |

---

## 3. DMA (Direct Memory Access)

**How DMA works:**
```
1. CPU programs DMA controller:
   - Source: device address
   - Destination: memory address
   - Size: number of bytes
   - Direction: device→memory or memory→device

2. DMA controller takes over the bus
   - Transfers data directly between device and memory
   - CPU is free to do other work (or may be paused briefly for bus cycles)

3. When complete:
   - DMA controller interrupts the CPU
   - CPU reads result from memory
```

**Benefits:**
- Frees CPU from managing each byte of transfer
- Much better throughput for large transfers (disk, network)
- CPU only involved at start and end of transfer

---

## 4. Performance of I/O

### I/O Bandwidth
```
Bandwidth = Data_Size / Transfer_Time
```

### I/O Overhead with Interrupt
```
Overhead = Interrupt_handling_time + DMA_setup_time + (transfer_size / device_bandwidth)
```

---

## 5. Memory-Mapped vs Port-Mapped I/O

| | Memory-Mapped | Port-Mapped |
|--|--------------|------------|
| Address space | Shared with memory | Separate I/O space |
| Instructions | LW/SW (load/store) | IN/OUT |
| Hardware | Simpler | Needs extra logic |
| Flexibility | High | Limited |
| Examples | MIPS, ARM | x86 (legacy) |

---

## 6. Quick Self-Test

**Q1:** What are the three main I/O methods? Which is best for transferring a 1MB file from disk?

**Q2:** What is memory-mapped I/O?

**Q3:** A DMA transfer moves 4KB at 100MB/s. How long does the transfer take?

---

### Answers

**Q1:** Polling, Interrupt-driven, DMA. **DMA** is best for large transfers — minimal CPU involvement, high throughput.

**Q2:** Device control registers are mapped into the processor's regular memory address space. The CPU accesses them using normal load/store instructions — no special I/O instructions needed.

**Q3:** `Time = 4KB / 100MB/s = 4096 / (100 × 1024 × 1024) ≈ 4096 / 104,857,600 ≈ 39 microseconds`
