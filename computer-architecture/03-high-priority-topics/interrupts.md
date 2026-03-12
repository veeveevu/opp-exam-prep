# 🔔 Interrupts — Study Guide

> 🟡 **HIGH PRIORITY** — Interrupt handling sequence and exception types are commonly tested.

---

## 1. Core Concepts

- **Interrupts** are signals that divert the CPU from its current execution to handle an external or internal event.
- **Asynchronous interrupts** (hardware interrupts): caused by external devices (keyboard, timer, network).
- **Synchronous exceptions** (traps/faults): caused by the executing instruction itself (divide by zero, page fault, system call).
- When an interrupt occurs, the CPU:
  1. Finishes the current instruction (or abandons it for faults)
  2. Saves processor state (PC, registers)
  3. Jumps to the **Interrupt Service Routine (ISR)**
  4. Executes ISR
  5. Restores state and resumes normal execution
- Interrupts enable **efficient I/O** — CPU is only involved when the device needs attention, not constantly polling.

---

## 2. Key Concepts & Terminology

| Term | Definition |
|------|-----------|
| **Interrupt** | External asynchronous signal requesting CPU attention |
| **Exception** | Internal synchronous event caused by instruction (fault, trap, abort) |
| **Trap** | Intentional exception (system call via `syscall` instruction) |
| **Fault** | Correctable exception — handler fixes problem, retries instruction (e.g., page fault) |
| **Abort** | Unrecoverable error (e.g., hardware failure) |
| **ISR / Interrupt Handler** | OS routine that services an interrupt |
| **Interrupt Vector Table** | Table of ISR addresses, indexed by interrupt number |
| **Interrupt Priority** | Higher priority interrupts can preempt lower priority ones |
| **Context Switch** | Saving and restoring full processor state |
| **Masking** | Disabling certain interrupts temporarily (to prevent nesting) |

---

## 3. Visual Diagrams

### Interrupt Handling Flow
```
Normal Execution             ISR (Interrupt Service Routine)
─────────────────            ───────────────────────────────
Instruction 1
Instruction 2                ┌─ Save context (PC, registers, flags)
Instruction 3 ─── Interrupt ─►  Handle the interrupt event
Instruction 4               │   (e.g., read byte from keyboard)
   ...        ◄─────────────┘   Restore context
                                 RETE / RTI (return from interrupt)
```

### Exception Types (MIPS)
```
Cause Register:
 ┌─────────────────────────────────────┐
 │   ExcCode field (5 bits) = type    │
 ├────────────┬────────────────────────┤
 │  Code 0    │  External Interrupt    │
 │  Code 4    │  Address load error    │
 │  Code 5    │  Address store error   │
 │  Code 8    │  Syscall               │
 │  Code 10   │  Reserved instruction  │
 │  Code 12   │  Overflow              │
 └────────────┴────────────────────────┘
```

### Interrupt Priority Levels
```
Priority  Level  Example
  High ─►  7     Power failure, hardware error
           6     Disk I/O complete
           5     Network packet received
           4     Keyboard/mouse
           3     Timer
           2     Software interrupts
   Low ─►  1     Normal program execution
```

---

## 4. Interrupt Handling Steps (Detailed)

### Step-by-step for MIPS:
1. **Interrupt occurs** (external or exception)
2. **CPU completes current instruction** (or abandons for fault)
3. **Hardware saves EPC** (Exception Program Counter = address to return to)
4. **Cause register** is set with the exception code
5. **PC ← exception handler address** (e.g., 0x80000180 in MIPS)
6. **Handler runs** (OS code):
   - Saves all registers (context save)
   - Determines exception type from Cause register
   - Dispatches to appropriate handler
   - Fixes the problem (for faults) or performs service (for I/O)
   - Restores all registers (context restore)
7. **ERET instruction** — return from exception, PC ← EPC

---

## 5. I/O and Interrupt Comparison

### Polling vs Interrupt-Driven vs DMA
```
Polling (Busy Wait):
  CPU ──loop──► Check device status ──► Not ready ──► loop
                                        Ready ──► Read data

Interrupt-Driven:
  CPU ────────────────────────────────► Execute program
       ◄──── Interrupt ─────────────── Device signals when ready
             Handle interrupt
             Return to program

DMA (Direct Memory Access):
  CPU ──► Tell DMA controller: read N bytes from device to address X
  CPU ────────────────────────────────► Execute other program
       ◄──── Interrupt ─────────────── DMA signals when transfer done
```

---

## 6. Worked Examples

### Example 1: Interrupt Handling Sequence
**Scenario:** CPU is executing instruction at PC=1000. A keyboard interrupt arrives. The keyboard ISR is at address 5000.

1. CPU completes instruction at 1000
2. EPC ← 1004 (address of next instruction to return to)
3. Cause ← keyboard interrupt code
4. PC ← 5000 (ISR address)
5. ISR saves $ra, $t0-$t9 to stack
6. ISR reads keyboard data from I/O register
7. ISR signals interrupt controller (acknowledgment)
8. ISR restores registers from stack
9. ERET executes → PC ← 1004 (resume from EPC)
10. CPU continues executing at 1004

### Example 2: Exception vs Interrupt
**Divide by zero** (exception):
- Synchronous: occurs at a specific instruction
- EPC = address of dividing instruction
- Handler terminates process or reports error

**Timer interrupt** (interrupt):
- Asynchronous: occurs at any time
- EPC = address of next instruction to execute
- Handler updates system time, may trigger context switch

---

## 7. Common Exam Questions

### "What is the difference between an interrupt and an exception?"
- **Interrupt:** External, asynchronous signal from hardware device
- **Exception:** Internal, synchronous event caused by the executing instruction
- Both redirect CPU to a handler routine; differ in cause and timing

### "List the steps the CPU takes when handling an interrupt"
1. Finish current instruction
2. Save PC (EPC) and status flags
3. Set Cause register
4. Jump to interrupt handler
5. Handler saves registers, services interrupt
6. Handler restores registers
7. Return to interrupted code (ERET)

### "Compare polling vs interrupt-driven I/O"
| | Polling | Interrupt-Driven |
|--|---------|-----------------|
| CPU involvement | Constant | Only during transfer |
| Latency | Low | Slightly higher |
| CPU efficiency | Low | High |
| Complexity | Simple | Complex |
| Best for | Fast I/O, real-time | Slow/unpredictable I/O |

---

## 8. Quick Self-Test

**Q1:** What is the difference between a fault and a trap?

**Q2:** Why must an interrupt handler save and restore all registers it uses?

**Q3:** What is the purpose of the EPC register?

**Q4:** When a page fault occurs, is the faulting instruction restarted or skipped? Why?

**Q5:** Which is more CPU-efficient: polling or interrupt-driven I/O? Why?

---

### Answers

**A1:** 
- **Fault:** Unintentional, correctable exception (page fault). Handler fixes the problem, then **retries the faulting instruction**.
- **Trap:** Intentional exception (system call). Handler performs a service, then continues at the **next instruction**.

**A2:** The interrupted program doesn't know an interrupt occurred. If the handler modifies registers without restoring them, the program resumes with corrupted register values — a hard-to-debug bug.

**A3:** EPC (Exception Program Counter) stores the address the CPU will return to after the interrupt handler finishes. For faults, it's the faulting instruction (to retry). For traps/interrupts, it's the next instruction.

**A4:** **Restarted** — because the page wasn't in memory when accessed. The handler loads the page, then the instruction needs to execute again to actually access the data.

**A5:** **Interrupt-driven I/O** — with polling, the CPU wastes cycles checking device status in a loop. With interrupts, the CPU executes useful work until the device is actually ready and signals via interrupt.
