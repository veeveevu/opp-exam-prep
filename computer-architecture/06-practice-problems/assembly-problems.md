# 🔣 Assembly Language Practice Problems

---

## Problem 1 — Reading Assembly

What does this MIPS code compute? (Assume $a0 = n, result in $v0)

```assembly
ADDI $v0, $zero, 0
ADDI $t0, $zero, 1
loop:
  SLT  $t1, $a0, $t0
  BNE  $t1, $zero, done
  ADD  $v0, $v0, $t0
  ADDI $t0, $t0, 1
  J    loop
done:
```

**a)** Trace through with $a0 = 4. What are the values of $v0 and $t0 at each loop iteration?

**b)** What mathematical function does this compute?

---

## Problem 2 — Writing Assembly

Translate the following C code to MIPS assembly:

```c
// a = $s0, b = $s1, c = $s2, result = $s3
if (a < b) {
    c = a + b;
} else {
    c = a - b;
}
result = c * 2;  // Use add for multiply by 2
```

---

## Problem 3 — Array Access

Translate to MIPS assembly:

```c
// int A[] has base address in $s0
// int B[] has base address in $s1
// n = $s2 (number of elements)
// Sum of A[i] + B[i] for all i, stored in $s3

int sum = 0;
for (int i = 0; i < n; i++) {
    sum = sum + A[i] + B[i];
}
```

---

## Problem 4 — Instruction Encoding

Encode the following MIPS instruction in 32-bit binary and hexadecimal:

`ADD $s1, $s2, $s3`

MIPS R-type encoding:
- opcode: 000000
- rs ($s2): 10010 (register 18)
- rt ($s3): 10011 (register 19)
- rd ($s1): 10001 (register 17)
- shamt: 00000
- funct (ADD): 100000

---

## Problem 5 — Instruction Decoding

Decode the following 32-bit MIPS instruction: `0x8C240004`

Binary: `1000 1100 0010 0100 0000 0000 0000 0100`

**a)** What is the opcode? (bits [31:26])

**b)** What instruction type is this (R/I/J)?

**c)** What are the rs, rt, and immediate fields?

**d)** What does this instruction do? (LW opcode = 100011 = 0x23)

---

## Problem 6 — Branch Target Calculation

A BEQ instruction at address `0x00400028` has an offset field of `0x000A` (decimal 10).

**a)** Is the branch forward or backward?

**b)** What is the target address?

**c)** Write the instruction in MIPS assembly (assume branch to label LOOP).

---

## Problem 7 — Stack and Function Call

Write MIPS assembly for a function `sum_squares(n)` that returns 1² + 2² + ... + n².

Argument: n in $a0
Return value in $v0

Remember to:
- Save $ra on the stack (if making nested calls — or use iterative approach)
- Save any $s registers you use
- Restore all saved registers before returning

---

## Problem 8 — Recursive Function (Advanced)

Write MIPS assembly for a **recursive** factorial function:

```c
int factorial(int n) {
    if (n <= 1) return 1;
    return n * factorial(n-1);
}
```

Argument: n in $a0
Return value in $v0

*(Hint: Must save $ra and $a0 on stack before recursive call)*

---

*See [SOLUTIONS.md](SOLUTIONS.md) for complete solutions.*
