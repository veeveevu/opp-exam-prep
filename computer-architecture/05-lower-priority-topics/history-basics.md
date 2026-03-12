# 📜 History & Basics — Study Guide

> ⚪ **LOWER PRIORITY** — Context and motivation; skim for key milestones.

---

## 1. Key Historical Milestones

| Era | Development |
|-----|------------|
| 1940s | ENIAC — first electronic general-purpose computer; vacuum tubes |
| 1947 | Transistor invented (Bell Labs) — replaces vacuum tubes |
| 1958 | Integrated circuit (IC) invented |
| 1965 | Moore's Law — transistor count doubles every ~2 years |
| 1971 | Intel 4004 — first commercial microprocessor |
| 1978 | Intel 8086 — x86 architecture begins |
| 1981 | IBM PC |
| 1985 | MIPS R2000 — classic RISC processor |
| 1993 | Intel Pentium — superscalar, out-of-order execution |
| 2000s | Multi-core era begins (clock scaling hits power wall) |
| 2010s | GPUs, accelerators, heterogeneous computing |

---

## 2. The Performance Revolution — Why Things Changed

### Moore's Law
- **Observation:** Number of transistors on a chip doubles approximately every 2 years
- **Consequence (1980s-2000s):** Clock speeds increased proportionally
- **Current status:** Physical limits slowing transistor scaling (~2nm process nodes)

### The Power Wall (circa 2004)
- **Problem:** Increasing clock frequency increases power exponentially: `P = C × V² × f`
- **Solution:** Stop increasing frequency; add more cores instead
- **Result:** Multi-core processors (2, 4, 8, 16+ cores) became standard

### Memory Wall
- CPU speed has grown much faster than DRAM speed
- **Gap:** CPU does ~4 cycles/instruction; DRAM access = 100-300 cycles
- **Solution:** Memory hierarchy (caches) to bridge this gap

---

## 3. Fundamental Computer Organization (von Neumann Architecture)

```
┌──────────────────────────────────────────┐
│              von Neumann Machine         │
│                                          │
│  ┌──────────┐        ┌─────────────┐    │
│  │   CPU    │◄──────►│   Memory    │    │
│  │ (Control │        │ (Programs + │    │
│  │  + ALU)  │        │   Data)     │    │
│  └──────────┘        └─────────────┘    │
│        │                                 │
│        ▼                                 │
│  ┌──────────┐                           │
│  │   I/O    │                           │
│  │ Devices  │                           │
│  └──────────┘                           │
└──────────────────────────────────────────┘

Key insight: Programs stored in same memory as data
(stored-program concept) — foundation of all modern computers
```

---

## 4. ISA (Instruction Set Architecture)

**ISA** defines the interface between software and hardware:
- Which instructions exist and what they do
- How many registers, what size
- Memory addressing modes
- Data types supported
- Calling conventions

**ISA is a contract** — software compiled for x86 runs on any x86 processor regardless of microarchitecture implementation.

| ISA | Chips |
|-----|-------|
| x86-64 | Intel Core, AMD Ryzen |
| ARM v8 | Apple M-series, Qualcomm Snapdragon |
| RISC-V | Open source; academic + emerging |
| MIPS | Classic academic RISC |

---

## 5. Quick Self-Test

**Q1:** What is Moore's Law and is it still holding?

**Q2:** Why did the industry shift from increasing clock speeds to adding more cores around 2004?

**Q3:** What is the von Neumann bottleneck?

---

### Answers

**Q1:** Moore's Law observes that transistor count doubles approximately every 2 years. It is **slowing** — not strictly holding anymore as we approach physical limits, though density still improves (slower rate).

**Q2:** Power consumption scales with frequency cubed (approximately). Doubling frequency would require much more power and generate too much heat. Multi-core allows more throughput at the same power budget.

**Q3:** The von Neumann bottleneck: the single bus between CPU and memory limits performance because both instructions and data share the same path. The CPU can compute much faster than it can fetch data from memory.
