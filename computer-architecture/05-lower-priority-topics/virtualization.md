# 🖥️ Virtualization — Study Guide

> ⚪ **LOWER PRIORITY** — Understand the basic concept and why hardware support helps.

---

## 1. Core Concepts

- **Virtualization** creates an abstract version of hardware, allowing multiple operating systems to run on one physical machine.
- A **hypervisor** (Virtual Machine Monitor, VMM) manages virtual machines (VMs).
- **Type 1 hypervisor (bare-metal):** Runs directly on hardware (VMware ESXi, Xen, KVM).
- **Type 2 hypervisor (hosted):** Runs on top of a host OS (VirtualBox, VMware Workstation).
- **Hardware virtualization support** (Intel VT-x, AMD-V) makes virtualization efficient by adding a "guest mode" to the CPU.

---

## 2. Types of Virtualization

| Type | Description | Example |
|------|-------------|---------|
| **Full Virtualization** | Guest OS unmodified; hypervisor traps privileged instructions | VMware, KVM |
| **Paravirtualization** | Guest OS modified to call hypervisor instead of HW | Xen (classic), some KVM |
| **OS-level (Containers)** | Single OS kernel; isolated namespaces | Docker, LXC |
| **Hardware-assisted** | CPU has VMX/SVM instructions | Intel VT-x, AMD-V |

---

## 3. Key Challenge: Privileged Instructions

**Problem:** Guest OS tries to execute privileged instructions (modify page tables, handle interrupts). On real hardware, only OS-level code can do this.

**Solutions:**
1. **Trap and emulate:** Privileged instructions cause a trap → hypervisor handles them
2. **Binary translation:** Hypervisor dynamically rewrites guest binary, replacing privileged instructions
3. **Hardware support (VT-x):** New CPU mode for guest execution; privileged instructions automatically trapped to hypervisor

---

## 4. Virtualization vs Containers

| | Virtual Machines | Containers |
|--|----------------|-----------|
| Isolation level | Hardware | OS (kernel) |
| OS overhead | Full OS per VM | Shared kernel |
| Boot time | Seconds | Milliseconds |
| Security isolation | Stronger | Weaker |
| Density | Lower | Higher |
| Examples | VMware, VirtualBox | Docker, Kubernetes |

---

## 5. Virtual Memory in Virtualized Systems

**Challenge:** Guest OS manages virtual→physical translation, but "physical" addresses in the VM are not real physical addresses.

**Solutions:**
1. **Shadow page tables:** Hypervisor maintains page tables that map guest virtual → host physical directly
2. **Extended Page Tables (EPT/NPT):** Hardware support for two-level address translation (guest virtual → guest physical → host physical)

---

## 6. Quick Self-Test

**Q1:** What is the difference between a Type 1 and Type 2 hypervisor?

**Q2:** Why is hardware virtualization support (VT-x/AMD-V) valuable?

**Q3:** What is the main advantage of containers over full VMs?

---

### Answers

**Q1:** Type 1 (bare-metal) runs directly on hardware — more efficient, used in data centers. Type 2 (hosted) runs on top of a host OS — easier to set up, used on desktops.

**Q2:** Without hardware support, the hypervisor must trap and emulate every privileged instruction the guest OS executes, which is slow. With hardware support (VT-x), the CPU handles trapping automatically and efficiently, dramatically reducing overhead.

**Q3:** Containers share the host OS kernel — no need to boot a separate OS. This gives much lower overhead (CPU, RAM, startup time) and higher VM density on the same hardware.
