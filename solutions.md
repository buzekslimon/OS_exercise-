# Operating Systems Exercises - Solutions

## Exercise 11: Deadlock Detection in Resource Allocation Graphs

### Problem Statement
For each of the resource allocation graphs shown, determine whether a deadlock can exist or not. If a deadlock exists, explain how it can be prevented.

### General Deadlock Detection Method

To detect deadlock in a Resource Allocation Graph (RAG), we need to check for **cycles**:

1. **For Single-Instance Resources**: A cycle in the graph indicates a deadlock.
2. **For Multi-Instance Resources**: A cycle is a necessary but not sufficient condition for deadlock. We need to use the Banker's algorithm or cycle detection with resource counting.

### Deadlock Detection Algorithm

**Step 1**: Identify all processes and resources in the graph.

**Step 2**: Look for cycles in the graph:
- A **request edge** goes from a process to a resource (P → R): Process P is waiting for resource R
- An **assignment edge** goes from a resource to a process (R → P): Resource R is allocated to process P

**Step 3**: If a cycle exists:
- Check if all resources in the cycle have only one instance
- If yes → **Deadlock exists**
- If no → May or may not be a deadlock (need further analysis)

### Four Necessary Conditions for Deadlock

For deadlock to occur, ALL four conditions must hold simultaneously:

1. **Mutual Exclusion**: At least one resource must be held in a non-sharable mode
2. **Hold and Wait**: A process holding at least one resource is waiting to acquire additional resources held by other processes
3. **No Preemption**: Resources cannot be forcibly taken from a process
4. **Circular Wait**: A circular chain of processes exists, where each process holds a resource needed by the next process

### Deadlock Prevention Strategies

To prevent deadlock, we can break any one of the four necessary conditions:

| Condition | Prevention Strategy |
|-----------|---------------------|
| **Mutual Exclusion** | Make resources sharable where possible (e.g., read-only files) |
| **Hold and Wait** | Require processes to request all resources at once before execution, or release all held resources before requesting new ones |
| **No Preemption** | Allow the OS to preempt resources from processes (e.g., using rollback mechanisms) |
| **Circular Wait** | Impose a total ordering on resource types and require processes to request resources in increasing order |

### Example Analysis (Generic RAG Patterns)

**Pattern 1: Simple Cycle with Single Instances**
```
P1 → R1 → P2 → R2 → P1
```
- **Result**: DEADLOCK EXISTS
- P1 holds R2 and waits for R1
- P2 holds R1 and waits for R2
- **Prevention**: Break circular wait by ordering resources (always request R1 before R2)

**Pattern 2: Cycle with Multiple Instances**
```
R1 (2 instances) allocated to P1 and P2
P1 and P2 both request R2 (1 instance held by P3)
P3 requests R1
```
- **Result**: Need to check if reduction sequence exists
- If a process can complete and release resources → No deadlock
- If no process can complete → DEADLOCK

**Pattern 3: No Cycle**
```
P1 → R1 → P2
P3 → R2
```
- **Result**: NO DEADLOCK (no circular wait)

---

## Exercise 12: Two-Level Paging System Calculations

### Problem Statement
Consider a memory system with the following specifications:
- **Virtual Address Space**: 32-bit addressing
- **Physical Memory Size**: 1 GB
- **Page Size**: 4 KB
- **Page Table Entry (PTE) Size**: 4 bytes
- **Page Table Structure**: Two-level hierarchical
- **Constraint**: Each page table must fit exactly in one memory page

### Given Information Summary

| Parameter | Value | In Bytes/Bits |
|-----------|-------|---------------|
| Virtual Address | 32 bits | - |
| Physical Memory | 1 GB | 2³⁰ bytes |
| Page Size | 4 KB | 2¹² bytes = 4096 bytes |
| PTE Size | 4 bytes | 32 bits |

---

### Part (a): Number of Offset Bits in Logical Address

**Solution:**

The offset within a page is determined by the page size.

```
Page Size = 4 KB = 4 × 1024 bytes = 4096 bytes = 2¹² bytes
```

To address any byte within a 4 KB page, we need:

```
Number of offset bits = log₂(Page Size)
                      = log₂(4096)
                      = log₂(2¹²)
                      = 12 bits
```

**Answer: 12 bits**

---

### Part (b): Number of Bits for Page Numbers

**Solution:**

In a 32-bit virtual address:

```
Total bits = Page number bits + Offset bits
32 = Page number bits + 12
Page number bits = 32 - 12 = 20 bits
```

**Answer: 20 bits for page numbers**

---

### Part (c) and (d): Number of Entries in Each Page Table

**Solution:**

Since each page table must fit in exactly one page:

```
Page Table Size = Page Size = 4 KB = 4096 bytes
```

Each Page Table Entry (PTE) is 4 bytes, so:

```
Number of entries per page table = Page Table Size / PTE Size
                                 = 4096 bytes / 4 bytes
                                 = 1024 entries
                                 = 2¹⁰ entries
```

**Answer: 1024 entries (2¹⁰) per page table**

---

### Part (e): Bits for First-Level and Second-Level Page Indices

**Solution:**

Since each page table has 1024 = 2¹⁰ entries, we need 10 bits to index into each table:

```
Bits for each level index = log₂(number of entries)
                          = log₂(1024)
                          = log₂(2¹⁰)
                          = 10 bits
```

**Verification:**
```
First-level index bits + Second-level index bits = Total page number bits
10 + 10 = 20 ✓
```

This matches our answer from part (b).

**Answer:**
- **First-level (outer) page index**: 10 bits
- **Second-level (inner) page index**: 10 bits

---

### Complete Virtual Address Structure

```
|<-------- 32-bit Virtual Address -------->|

| First-Level Index | Second-Level Index | Page Offset |
|      10 bits      |      10 bits       |   12 bits   |
|    (bits 31-22)   |    (bits 21-12)    | (bits 11-0) |
```

### Address Translation Process

```
                    Virtual Address (32 bits)
                    ┌──────────┬──────────┬─────────────┐
                    │  P1 (10) │  P2 (10) │ Offset (12) │
                    └────┬─────┴────┬─────┴──────┬──────┘
                         │          │            │
                         ▼          │            │
    ┌─────────────────────────┐     │            │
    │   First-Level Page      │     │            │
    │   Table (Page Directory)│     │            │
    │   (1024 entries)        │     │            │
    │   ┌────────────────┐    │     │            │
    │   │     ...        │    │     │            │
P1──┼──►│  Entry P1  ────┼────┼─────┤            │
    │   │     ...        │    │     │            │
    │   └────────────────┘    │     │            │
    └─────────────────────────┘     │            │
                                    ▼            │
              ┌─────────────────────────┐        │
              │   Second-Level Page     │        │
              │   Table                 │        │
              │   (1024 entries)        │        │
              │   ┌────────────────┐    │        │
              │   │     ...        │    │        │
         P2───┼──►│  Entry P2  ────┼────┼────────┤
              │   │     ...        │    │        │
              │   └────────────────┘    │        │
              └─────────────────────────┘        │
                          │                      │
                          ▼                      ▼
              ┌─────────────────────────────────────┐
              │       Physical Address              │
              │  Frame Number (18 bits) + Offset    │
              └─────────────────────────────────────┘
```

### Summary Table

| Question | Answer |
|----------|--------|
| (a) Offset bits | **12 bits** |
| (b) Page number bits | **20 bits** |
| (d) Entries per page table | **1024 entries (2¹⁰)** |
| (e) First-level index bits | **10 bits** |
| (e) Second-level index bits | **10 bits** |

### Additional Calculations (for completeness)

**Physical Address Bits:**
```
Physical Memory = 1 GB = 2³⁰ bytes
Physical Address bits = log₂(2³⁰) = 30 bits
```

**Number of Physical Frames:**
```
Number of frames = Physical Memory / Page Size
                 = 2³⁰ / 2¹²
                 = 2¹⁸ frames
                 = 262,144 frames
```

**Frame Number Bits in PTE:**
```
Frame number bits = log₂(number of frames)
                  = log₂(2¹⁸)
                  = 18 bits
```

The remaining bits in each 32-bit PTE (14 bits) can be used for:
- Valid/Invalid bit
- Read/Write/Execute permissions
- Dirty bit
- Referenced bit
- Other control flags

---

## Summary of All Answers

### Exercise 11
- Use cycle detection to identify deadlocks in resource allocation graphs
- A cycle with single-instance resources indicates deadlock
- Prevention methods: Break mutual exclusion, hold-and-wait, no preemption, or circular wait

### Exercise 12

| Part | Question | Answer |
|------|----------|--------|
| a | Offset bits in logical address | **12 bits** |
| b | Bits for page numbers | **20 bits** |
| d | Number of entries per page table | **1024 entries** |
| e | First-level index bits | **10 bits** |
| e | Second-level index bits | **10 bits** |
