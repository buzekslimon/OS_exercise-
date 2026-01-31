# Operating Systems Exercises - Solutions

## Exercise 11: Deadlock Detection in Resource Allocation Graphs

### Problem Statement
Determine whether deadlock can exist in the given resource allocation graphs. If deadlock exists, explain how to prevent it.

### Deadlock Detection Theory

A **deadlock** occurs in a Resource Allocation Graph (RAG) when there is a **cycle** in the graph AND the resources involved have only **single instances**.

**Key Rules for Deadlock Detection in RAGs:**

1. **For Single-Instance Resources:** A cycle in the graph implies deadlock.
2. **For Multi-Instance Resources:** A cycle is a necessary but not sufficient condition. We must use the **cycle detection algorithm** or **Banker's algorithm** to confirm deadlock.

### General Analysis Method

To analyze any Resource Allocation Graph:

1. **Identify all processes** (circles/ovals in the graph)
2. **Identify all resources** (rectangles/squares with dots representing instances)
3. **Trace request edges** (Process → Resource): Process is waiting for resource
4. **Trace assignment edges** (Resource → Process): Resource is held by process
5. **Look for cycles** in the wait-for relationship

### Deadlock Prevention Methods

If deadlock exists or is possible, it can be prevented using these strategies:

#### 1. **Mutual Exclusion Prevention**
- Make resources shareable when possible
- Not always practical (e.g., printers, CPU)

#### 2. **Hold and Wait Prevention**
- Require processes to request all resources at once before execution
- Or release all held resources before requesting new ones
- **Disadvantage:** Low resource utilization

#### 3. **No Preemption Prevention**
- If a process holding resources requests another that cannot be allocated:
  - Release all currently held resources
  - Process restarts when all resources available
- Works for resources whose state can be saved (CPU, memory)

#### 4. **Circular Wait Prevention**
- Impose a total ordering on all resource types
- Processes must request resources in increasing order
- **Most practical method**

### Example Analysis Framework

**For a graph with processes P1, P2, P3 and resources R1, R2, R3:**

```
If we have:
- P1 holds R1, requests R2
- P2 holds R2, requests R3  
- P3 holds R3, requests R1

This forms a cycle: P1 → R2 → P2 → R3 → P3 → R1 → P1
Result: DEADLOCK EXISTS (assuming single instances)
```

**Prevention for this example:**
- Order resources: R1 < R2 < R3
- Processes must request in this order
- P3 would need R1 before R3, breaking the cycle

---

## Exercise 12: Two-Level Hierarchical Page Table

### Problem Statement
Consider a memory system with:
- **Virtual Address Space:** 32-bit addressing
- **Physical Memory Size:** 1 GB
- **Page Size:** 4 KB
- **Page Table Entry (PTE) Size:** 4 bytes
- **Structure:** Two-level hierarchical page table
- **Constraint:** Each page table fits exactly in one memory page

### Solutions

#### Part (a): Number of Offset Bits in Logical Address

The offset bits determine the position within a page.

```
Page Size = 4 KB = 4 × 1024 bytes = 4096 bytes = 2^12 bytes
```

**Answer: Offset = 12 bits**

This is because we need 12 bits to address all 4096 (2^12) bytes within a single page.

---

#### Part (b): Bits Remaining for Page Numbers

```
Total Virtual Address Bits = 32 bits
Offset Bits = 12 bits

Bits for Page Numbers = 32 - 12 = 20 bits
```

**Answer: 20 bits remain for page numbers**

These 20 bits will be split between the first-level and second-level page table indices.

---

#### Part (c) & (d): Number of Entries in Each Page Table

Each page table must fit in exactly one memory page (4 KB).

```
Page Table Size = Page Size = 4 KB = 4096 bytes
Page Table Entry Size = 4 bytes

Number of Entries per Page Table = Page Table Size / PTE Size
                                 = 4096 bytes / 4 bytes
                                 = 1024 entries
                                 = 2^10 entries
```

**Answer: Each page table has 1024 entries (2^10 entries)**

---

#### Part (e): Bits for First-Level and Second-Level Page Indices

Since each page table has 1024 (2^10) entries, we need 10 bits to index into each table.

```
Entries per table = 1024 = 2^10
Bits needed per index = 10 bits
```

**Verification:**
```
First-Level Index bits + Second-Level Index bits = Total Page Number bits
10 bits + 10 bits = 20 bits ✓
```

**Answer:**
- **First-Level (Outer) Page Index:** 10 bits
- **Second-Level (Inner) Page Index:** 10 bits

---

### Complete Virtual Address Structure (32-bit)

```
┌─────────────────┬─────────────────┬────────────────┐
│  First-Level    │  Second-Level   │     Page       │
│  Page Index     │  Page Index     │     Offset     │
│    (P1)         │     (P2)        │     (d)        │
├─────────────────┼─────────────────┼────────────────┤
│    10 bits      │    10 bits      │    12 bits     │
│  (bits 31-22)   │  (bits 21-12)   │  (bits 11-0)   │
└─────────────────┴─────────────────┴────────────────┘
         └──────── 20 bits ────────┘
                Page Number
```

### Address Translation Process

1. **Extract P1** (bits 31-22): Index into the outer page table (page directory)
2. **Find base address** of the second-level page table
3. **Extract P2** (bits 21-12): Index into the inner page table
4. **Find frame number** from the page table entry
5. **Combine frame number with offset (d)** to get the physical address

### Additional Calculations

#### Total Number of Virtual Pages
```
Virtual Address Space = 2^32 bytes
Page Size = 2^12 bytes
Number of Virtual Pages = 2^32 / 2^12 = 2^20 = 1,048,576 pages
```

#### Physical Memory Frames
```
Physical Memory = 1 GB = 2^30 bytes
Page/Frame Size = 4 KB = 2^12 bytes
Number of Physical Frames = 2^30 / 2^12 = 2^18 = 262,144 frames
```

#### Bits Needed for Frame Number in PTE
```
Number of frames = 2^18
Bits for frame number = 18 bits

Remaining bits in PTE (for flags) = 32 - 18 = 14 bits
(Used for: Valid bit, Dirty bit, Referenced bit, Protection bits, etc.)
```

---

## Summary of Answers

### Exercise 11
- Deadlock exists when there is a cycle in RAG with single-instance resources
- Prevention methods: Resource ordering (circular wait prevention), hold-and-wait prevention, preemption, or mutual exclusion relaxation

### Exercise 12

| Question | Answer |
|----------|--------|
| (a) Offset bits | **12 bits** |
| (b) Page number bits | **20 bits** |
| (d) Entries per page table | **1024 entries (2^10)** |
| (e) First-level index bits | **10 bits** |
| (e) Second-level index bits | **10 bits** |

### Visual Summary of Address Format

```
32-bit Virtual Address:
┌──────────┬──────────┬──────────────┐
│ P1 (10)  │ P2 (10)  │ Offset (12)  │
└──────────┴──────────┴──────────────┘

Address Translation:
Page Directory [P1] → Page Table [P2] → Frame Number + Offset → Physical Address
```
