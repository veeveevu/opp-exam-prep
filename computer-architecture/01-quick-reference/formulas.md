# 📐 Formulas — All Calculations You Need

> **Print this page.** These formulas appear on almost every computer architecture exam.

---

## ⚡ Performance

### CPU Time
```
CPU Time = Instruction Count (IC) × CPI × Clock Cycle Time
CPU Time = IC × CPI / Clock Rate (frequency)
```

### CPI (Cycles Per Instruction)
```
CPI_effective = Σ (CPI_i × Frequency_i)

where Frequency_i = (count of instruction type i) / (total instruction count)
```

### Speedup
```
Speedup = Time_old / Time_new
         = (IC_old × CPI_old × CCT_old) / (IC_new × CPI_new × CCT_new)
```

### MIPS (Millions of Instructions Per Second)
```
MIPS = Instruction Count / (Execution Time × 10^6)
     = Clock Rate / (CPI × 10^6)
```
⚠️ MIPS is misleading — do not use to compare different ISAs.

### Amdahl's Law
```
Speedup_overall = 1 / ((1 − P) + P/S)

where:
  P = fraction of program that is improved (parallelizable)
  S = speedup of the improved part
```

**Key insight from Amdahl's Law:**
- Maximum speedup is limited by the non-parallelizable fraction: `1 / (1 − P)`
- Even infinite processors give limited gain

---

## 🗃️ Cache

### AMAT (Average Memory Access Time) — Single Level
```
AMAT = Hit Time + Miss Rate × Miss Penalty
```

### AMAT — Two Levels
```
AMAT = L1_Hit_Time + L1_Miss_Rate × (L2_Hit_Time + L2_Miss_Rate × Mem_Penalty)
```

### Direct-Mapped Index
```
Block Address = Byte Address / Block Size (integer division)
Index = Block Address mod Number_of_Blocks
```

### Address Bit Breakdown
```
Offset bits  = log₂(Block Size in bytes)
Index bits   = log₂(Number of sets)
Tag bits     = Total address bits − Index bits − Offset bits
```

### Cache Size
```
Cache Size = Number_of_Sets × Associativity × Block_Size
```

### Number of Sets
```
Number_of_Sets = Total_Blocks / Associativity
```

---

## 💾 Virtual Memory

### Address Breakdown
```
Page Offset bits = log₂(Page Size in bytes)
VPN bits         = Virtual Address bits − Page Offset bits
Number of pages  = 2^(VPN bits)
```

### Physical Address
```
Physical Address = PFN × Page_Size + Page_Offset
                 = (PFN << page_offset_bits) | page_offset
```

### Page Table Size
```
Page Table Size = Number_of_VPN_entries × PTE_Size_in_bytes
               = 2^(VPN_bits) × PTE_Size_in_bytes
```

### Multi-Level Page Table
```
Level-1 index bits = log₂(number of L1 entries)
Level-2 index bits = log₂(number of L2 entries)
Total VPN bits = L1_bits + L2_bits
```

---

## ⚙️ Pipelining

### Ideal Pipeline Speedup
```
Speedup_ideal = Number of pipeline stages (n)
```

### Real Pipeline Speedup
```
Speedup = n / (1 + stall_cycles_per_instruction)
```

### CPI with Stalls
```
CPI_pipeline = Ideal_CPI + Stall_cycles_per_instruction
             = 1 + (branch_freq × branch_penalty) + (load_use_freq × 1)
```

### Pipeline Execution Time (k instructions, n stages)
```
Time = (k + n − 1) × cycle_time
```

---

## 🔢 Buses & Memory

### Bandwidth
```
Bandwidth = Bus_Width × Bus_Frequency
          = (bits_per_transfer) / (time_per_transfer)
```

### Transfer Time
```
Transfer Time = (1 / Bus_Frequency) × (address_cycles + data_cycles)
```

---

## 🔄 Other Useful Formulas

### Hit Rate & Miss Rate
```
Hit Rate + Miss Rate = 1
Miss Rate = 1 − Hit Rate
```

### Execution Time with Memory Stalls
```
CPU Time = (CPU_cycles + Memory_stall_cycles) × Cycle_Time
Memory_stall_cycles = IC × mem_accesses_per_instruction × Miss_Rate × Miss_Penalty
```

### Branch Penalty Impact
```
CPI_with_branches = CPI_base + branch_freq × branch_penalty_cycles
```

---

## 📏 Useful Powers of 2 (Memorize These!)

| Value | Power of 2 |
|-------|-----------|
| 4 | 2² |
| 8 | 2³ |
| 16 | 2⁴ |
| 32 | 2⁵ |
| 64 | 2⁶ |
| 128 | 2⁷ |
| 256 | 2⁸ |
| 512 | 2⁹ |
| 1,024 (1K) | 2¹⁰ |
| 4,096 (4K) | 2¹² |
| 65,536 (64K) | 2¹⁶ |
| 1,048,576 (1M) | 2²⁰ |
| 1,073,741,824 (1G) | 2³⁰ |

---

## 📝 Unit Conversions

| Prefix | Symbol | Value |
|--------|--------|-------|
| Kilo | K | 2¹⁰ = 1,024 |
| Mega | M | 2²⁰ = 1,048,576 |
| Giga | G | 2³⁰ |
| Tera | T | 2⁴⁰ |
