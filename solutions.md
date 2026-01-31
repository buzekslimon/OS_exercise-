# Operating Systems Exercises - Solutions

## Exercise 11: Deadlock Detection in Resource Allocation Graphs

### Problem Statement
Determine whether deadlock can exist in the given resource allocation graphs. If deadlock exists, explain how to prevent it.

---

## Graph 1 Analysis (Left Graph)

### Resources and Processes:
- **R1**: 1 instance (single dot)
- **R2**: 2 instances (two dots)
- **R3**: 2 instances (two dots)
- **Processes**: T1, T2, T3, T4

### Current Allocation State:

| Process | Holds | Requests |
|---------|-------|----------|
| T1 | R1 (1) | R2 |
| T2 | R2 (1) | R3 |
| T3 | R2 (1), R3 (1) | R1 |
| T4 | R3 (1) | R2 |

### Resource Status:
- **R1**: 1/1 allocated (to T1) → 0 available
- **R2**: 2/2 allocated (to T2, T3) → 0 available
- **R3**: 2/2 allocated (to T3, T4) → 0 available

### Cycle Detection:

**Cycle Found: T1 → R2 → T3 → R1 → T1**

```
T1 waits for R2
    ↓
R2 is held by T3 (among others)
    ↓
T3 waits for R1
    ↓
R1 is held by T1
    ↓
Back to T1 (CYCLE!)
```

### Deadlock Analysis:

Can any process complete?
- **T1**: Needs R2, but R2 has 0 available → **BLOCKED**
- **T2**: Needs R3, but R3 has 0 available → **BLOCKED**
- **T3**: Needs R1, but R1 has 0 available → **BLOCKED**
- **T4**: Needs R2, but R2 has 0 available → **BLOCKED**

### ✅ Result: **DEADLOCK EXISTS**

All processes are blocked in a circular wait. No process can proceed.

### Prevention Methods for Graph 1:

1. **Resource Ordering**: Define order R1 < R2 < R3. T3 should request R1 before R2/R3.
2. **Hold and Wait Prevention**: T3 should request R1, R2, R3 all at once at the start.
3. **Preemption**: Allow preemption of R1 from T1 to give to T3.

---

## Graph 2 Analysis (Right Graph)

### Resources and Processes:
- **R1**: 2 instances (two dots)
- **R2**: 2 instances (two dots)
- **Processes**: T1, T2, T3

### Current Allocation State:

| Process | Holds | Requests |
|---------|-------|----------|
| T1 | R1 (1), R2 (1) | R2 |
| T2 | R1 (1) | R2 |
| T3 | R2 (1) | R1 |

### Resource Status:
- **R1**: 2/2 allocated (to T1, T2) → 0 available
- **R2**: 2/2 allocated (to T1, T3) → 0 available

### Cycle Detection:

**Cycle 1: T1 → R2 → T3 → R1 → T1**
```
T1 waits for R2 → R2 held by T3 → T3 waits for R1 → R1 held by T1
```

**Cycle 2: T2 → R2 → T3 → R1 → T2**
```
T2 waits for R2 → R2 held by T3 → T3 waits for R1 → R1 held by T2
```

### Deadlock Analysis:

Can any process complete?
- **T1**: Needs R2, but R2 has 0 available → **BLOCKED**
- **T2**: Needs R2, but R2 has 0 available → **BLOCKED**
- **T3**: Needs R1, but R1 has 0 available → **BLOCKED**

### ✅ Result: **DEADLOCK EXISTS**

All three processes are blocked. No process can release resources.

### Prevention Methods for Graph 2:

1. **Resource Ordering**: Define R1 < R2. All processes must request R1 before R2.
   - T3 violates this (holds R2, requests R1)
2. **Hold and Wait Prevention**: 
   - T1 should request all needed R1 and R2 instances at startup
   - T3 should request R1 and R2 together
3. **Preemption**: Preempt R2 from T3, allow T1 or T2 to complete first

---

## Summary for Exercise 11

| Graph | Deadlock? | Reason |
|-------|-----------|--------|
| Graph 1 | **YES** | Cycle T1→R2→T3→R1→T1, all processes blocked |
| Graph 2 | **YES** | Multiple cycles, all processes blocked |

### Best Prevention Strategy:
**Circular Wait Prevention** - Impose ordering on resources (R1 < R2 < R3) and require all processes to request resources in increasing order only

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

| Question | Answer | Calculation |
|----------|--------|-------------|
| (a) Offset bits | **12 bits** | log₂(4096) = 12 |
| (b) Page number bits | **20 bits** | 32 - 12 = 20 |
| (d) Entries per page table | **1024 entries (2^10)** | 4096 / 4 = 1024 |
| (e) First-level index bits | **10 bits** | log₂(1024) = 10 |
| (e) Second-level index bits | **10 bits** | log₂(1024) = 10 |

### Verification Check
```
P1 bits + P2 bits + Offset bits = 10 + 10 + 12 = 32 bits ✓
```

### Visual Summary of Address Format

```
32-bit Virtual Address:
┌──────────┬──────────┬──────────────┐
│ P1 (10)  │ P2 (10)  │ Offset (12)  │
│ bits     │ bits     │ bits         │
│ 31...22  │ 21...12  │ 11...0       │
└──────────┴──────────┴──────────────┘
    │           │            │
    │           │            └──→ Position within page (0-4095)
    │           └─────────────────→ Index into inner page table (0-1023)
    └─────────────────────────────→ Index into outer page table (0-1023)

Address Translation:
Page Directory [P1] → Page Table [P2] → Frame Number + Offset → Physical Address
```
