# 💰 Partial Credit Tips

> On a paper exam, partial credit is your best friend. Here's how to maximize it.

---

## 🎯 The Partial Credit Mindset

**Wrong:** "I don't know this completely, so I'll leave it blank."

**Right:** "I'll write what I know. Every step I get right earns points."

---

## 📝 How Examiners Award Partial Credit

Examiners typically allocate points to:
1. **Correct formula** — writing the right formula, even with wrong numbers
2. **Correct setup** — identifying the right variables
3. **Correct substitution** — plugging in numbers correctly
4. **Correct calculation** — arithmetic steps
5. **Correct answer** — final result with units

A common 5-point calculation question: 1 + 1 + 1 + 1 + 1 = 5 points. Get the formula right → 1 point minimum.

---

## ✅ Strategies to Maximize Partial Credit

### For Calculation Questions:
```
1. Write the formula FIRST (even if you're not sure)
2. Write each variable with its value: "Miss_Rate = 0.05"
3. Substitute into formula
4. Show each arithmetic step
5. Write the final answer with units

Example:
  Formula: AMAT = Hit_Time + Miss_Rate × Miss_Penalty   [1 pt]
  Values: HT=4, MR=0.1, MP=100                          [1 pt]
  AMAT = 4 + 0.1 × 100                                  [1 pt]
       = 4 + 10                                          [1 pt]
       = 14 cycles                                       [1 pt]
```

### For Explanation Questions:
- **Write a list** rather than paragraphs — examiners can check off each point
- **Use technical terms** — they're looking for keyword understanding
- **Don't overthink** — a correct partial explanation beats no explanation

### For Diagram Questions:
- **Draw something** — even an incorrect diagram shows understanding
- **Label everything** — labeled boxes with correct names earn points
- **Show the direction** — arrows showing data flow matter
- If the full diagram is too hard, draw the part you know

### For Comparison Questions:
- Use a **table format** — easy to grade, clear structure
- Include **at least 3 dimensions** of comparison
- Add a **one-sentence conclusion** — "Therefore, X is better when..."

---

## 🔧 The "I Don't Know" Toolkit

When you're truly stuck, use these techniques to earn partial credit:

### Technique 1: Write what you know about the topic
Even if you can't answer the specific question:
"Virtual memory allows programs to use more address space than physical memory. It uses page tables to map virtual to physical addresses. The TLB caches recent translations."

### Technique 2: Use a related formula you remember
If asked for CPI but you forgot one term: write what you have:
"CPU Time = IC × CPI × Clock Cycle Time. I don't recall the exact formula for memory stall CPI but know it involves miss rate and miss penalty."

### Technique 3: Make reasonable assumptions
"Assuming a 4KB page size (typical), the offset would be log₂(4096) = 12 bits..."
State your assumptions — examiners often give credit if reasoning is correct even with a different assumption.

### Technique 4: Describe the process even without the answer
"To calculate AMAT: (1) determine hit time from the cache spec, (2) determine miss rate from the access pattern, (3) determine miss penalty from the memory latency, (4) apply AMAT = HT + MR × MP."

---

## ⚠️ Things That LOSE Partial Credit

- **Blank space** — always write something
- **Crossed-out correct work** — don't cross out if you're not sure it's wrong
- **Multiple conflicting answers** — pick one and stick with it
- **No units** on final answer — examiners check
- **Ambiguous notation** — be clear about which answer is final

---

## 📊 Partial Credit Examples

### Cache Problem (10 points)
> "Calculate the AMAT for a cache with 4-cycle hit time, 10% miss rate, 80-cycle miss penalty. (4 pts) Then calculate the CPI if each instruction accesses memory once and base CPI = 1. (3 pts) Finally, calculate CPU time for 10⁸ instructions at 1 GHz. (3 pts)"

Even if you can't do part c:
- Part a correctly = 4 pts
- Part b correctly = 3 pts
- Part c: write "CPU Time = IC × CPI / Clock_Rate = 10⁸ × [CPI] / 10⁹" → 1-2 pts for setup

**Don't leave any part blank.**

### Pipeline Question (8 points)
> "Draw a pipeline timing diagram for 5 instructions with these hazards..."

Even if not all hazards are right:
- Draw the basic pipeline correctly = 2-3 pts
- Show correct stage labels = 1 pt
- Show some stalls in right places = 2-3 pts

---

## 💬 Phrases That Help

Use these to make your reasoning visible:
- "Using the formula X = ..."
- "Because [principle], therefore [conclusion]"
- "This is a RAW hazard because..."
- "Assuming [X] (standard assumption), ..."
- "The pipeline stalls because [reason]"

Visible reasoning = partial credit, even if the conclusion is wrong.
