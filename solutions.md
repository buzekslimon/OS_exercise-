# Operating Systems Exercises - Solutions

## Exercise 11: Deadlock Detection in Resource Allocation Graphs

### Problem Statement
For each of the resource allocation graphs shown, determine whether a deadlock can exist or not. If a deadlock exists, explain how it can be prevented.

---

## Graph 1 Analysis (Left Graph)

### Resource and Process Identification

**Resources:**
- R1: 1 instance (single dot)
- R2: 2 instances (two dots)
- R3: 2 instances (two dots)

**Processes:** T1, T2, T3, T4

### Arrow Analysis from Image (VERIFIED)

**Allocation Arrows (Resource → Process):**
| Arrow | Meaning |
|-------|---------|
| R1 → T1 | R1's only instance allocated to T1 |
| R2 → T3 | One instance of R2 allocated to T3 |
| R2 → T4 | One instance of R2 allocated to T4 |
| **R3 → (none)** | **NO allocation arrows from R3!** |

**Request Arrows (Process → Resource):**
| Arrow | Meaning |
|-------|---------|
| T1 → R2 | T1 requests R2 |
| T2 → R2 | T2 requests R2 |
| T2 → R3 | T2 requests R3 |
| T3 → R1 | T3 requests R1 |
| T4 → R3 | T4 requests R3 |

### Resource Availability (CRITICAL)
| Resource | Total | Allocated | **Available** |
|----------|-------|-----------|---------------|
| R1 | 1 | 1 (to T1) | **0** |
| R2 | 2 | 2 (to T3, T4) | **0** |
| R3 | 2 | **0** | **2** ← KEY! |

### State Summary
```
T1: holds R1(1), wants R2      → R2 has 0 available → WAITING
T2: holds nothing, wants R2,R3 → R2 has 0 available → WAITING  
T3: holds R2(1), wants R1      → R1 has 0 available → WAITING
T4: holds R2(1), wants R3      → R3 has 2 available → CAN PROCEED!
```

### Cycle Detection
There is a cycle: **T1 → R2 → T3 → R1 → T1**

However, for multi-instance resources, a cycle does NOT guarantee deadlock. We must use the graph reduction algorithm.

### Deadlock Analysis (Graph Reduction Method)

**Step 1:** Find a process that can complete
- T4 holds R2(1), wants R3
- R3 has **2 available instances** (no allocations from R3!)
- **T4 CAN COMPLETE** ✓

**Step 2:** T4 completes and releases R2
- R2: now has 1 available instance

**Step 3:** Check remaining processes
- T1 holds R1, wants R2 → R2 now available → **T1 CAN COMPLETE** ✓
- T1 releases R1 and R2

**Step 4:** Continue reduction
- T3 holds R2(1), wants R1 → R1 now available → **T3 CAN COMPLETE** ✓
- T2 wants R2, R3 → both available → **T2 CAN COMPLETE** ✓

**All processes can complete!**

### Result for Graph 1: ✅ NO DEADLOCK

**Reason:** Although a cycle exists (T1→R2→T3→R1→T1), **R3 has 2 available instances** (no allocation arrows from R3 in the graph). This allows T4 to proceed, which breaks the potential deadlock. The graph is fully reducible.

**Key Insight:** The critical observation is that R3 has NO allocation arrows in the image - only request arrows (from T2 and T4). This means R3's instances are all available.

---

## Graph 2 Analysis (Right Graph)

### Resource and Process Identification

**Resources:**
- R1: 2 instances (two dots)
- R2: 2 instances (two dots)

**Processes:** T1, T2, T3

### Current Allocations (Resource → Process)
| Resource | Allocated To |
|----------|--------------|
| R1 (2 instances) | T1, T2 (one each) |
| R2 (2 instances) | T3 (both instances) |

### Current Requests (Process → Resource)
| Process | Requesting |
|---------|------------|
| T1 | R2 |
| T2 | R2 |
| T3 | R1 |

### State Summary
```
T1: holds R1 (1 instance), wants R2
T2: holds R1 (1 instance), wants R2
T3: holds R2 (2 instances), wants R1
```

### Resource Availability
```
R1: 2 instances total, 2 allocated → 0 available
R2: 2 instances total, 2 allocated → 0 available
```

### Cycle Detection
Multiple cycles exist:
- **T1 → R2 → T3 → R1 → T1**
- **T2 → R2 → T3 → R1 → T2**

### Deadlock Analysis (Graph Reduction Method)

Check if any process can complete:

1. **T1**: Holds R1, wants R2
   - R2 has 0 available instances (both held by T3)
   - T1 **CANNOT proceed** ✗

2. **T2**: Holds R1, wants R2
   - R2 has 0 available instances (both held by T3)
   - T2 **CANNOT proceed** ✗

3. **T3**: Holds R2 (both), wants R1
   - R1 has 0 available instances (held by T1 and T2)
   - T3 **CANNOT proceed** ✗

**No process can proceed!**

### Result for Graph 2: ❌ DEADLOCK EXISTS

**Reason:** All processes are blocked in a circular wait:
- T1 and T2 are waiting for R2 (held by T3)
- T3 is waiting for R1 (held by T1 and T2)

The graph is NOT reducible.

---

## Deadlock Prevention Methods for Graph 2

### Method 1: Break Circular Wait (Resource Ordering)
Assign a total order to resources: R1 = 1, R2 = 2

**Rule:** Processes must request resources in increasing order.

- T3 should request R1 BEFORE requesting R2
- This prevents the circular wait condition

### Method 2: Break Hold and Wait
Require processes to request ALL needed resources at once:

- T1 requests R1 and nothing else → OK
- T2 requests R1 and nothing else → OK  
- T3 must request R1 AND R2 together at the start

If T3 cannot get both R1 and R2, it waits without holding anything.

### Method 3: Allow Preemption
If a process is waiting, preempt its resources:

- When T3 waits for R1, preempt R2 from T3
- Give R2 to T1 or T2, let them complete
- Then T3 can acquire R1 and R2

### Method 4: Deadlock Avoidance (Banker's Algorithm)
Use the Banker's algorithm to check if granting a request leads to a safe state before allocating resources.

---

## Summary Table for Exercise 11

| Graph | Deadlock? | Key Reason |
|-------|-----------|------------|
| **Graph 1** | **NO** | R3 has 2 available instances (no allocations) → T4 can proceed → breaks the cycle |
| **Graph 2** | **YES** | All resources (R1 and R2) fully allocated → no process can proceed → circular wait |

### Verification of Graph 1 Answer

**Why some analyses incorrectly say "DEADLOCK" for Graph 1:**
- They incorrectly assume R3 is fully allocated
- Looking at the actual image: R3 has NO allocation arrows (only request arrows from T2 and T4)
- R3 has 2 instances, 0 allocated → 2 available

**Proof that NO DEADLOCK exists:**
```
Initial: R1=0 avail, R2=0 avail, R3=2 avail
T4 gets R3 → completes → releases R2
After:   R1=0 avail, R2=1 avail, R3=2 avail
T1 gets R2 → completes → releases R1, R2  
After:   R1=1 avail, R2=2 avail, R3=2 avail
T3 gets R1 → completes
T2 gets R2, R3 → completes
ALL PROCESSES COMPLETE ✓
```

---

## General Deadlock Theory

### Four Necessary Conditions for Deadlock

For deadlock to occur, ALL four conditions must hold simultaneously:

1. **Mutual Exclusion**: At least one resource must be held in a non-sharable mode
2. **Hold and Wait**: A process holding at least one resource is waiting to acquire additional resources held by other processes
3. **No Preemption**: Resources cannot be forcibly taken from a process
4. **Circular Wait**: A circular chain of processes exists, where each process holds a resource needed by the next process

### Deadlock Prevention Strategies

| Condition | Prevention Strategy |
|-----------|---------------------|
| **Mutual Exclusion** | Make resources sharable where possible (e.g., read-only files) |
| **Hold and Wait** | Require processes to request all resources at once before execution |
| **No Preemption** | Allow the OS to preempt resources from processes |
| **Circular Wait** | Impose a total ordering on resource types |

### Key Point for Multi-Instance Resources

**A cycle in a Resource Allocation Graph with multi-instance resources does NOT necessarily mean deadlock.** We must use the graph reduction algorithm to determine if deadlock exists:

1. Find a process whose requests can be satisfied
2. Simulate that process completing and releasing its resources
3. Repeat until either all processes complete (no deadlock) or no process can proceed (deadlock)

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

### Memory Requirements Analysis

**First-Level Page Table (Page Directory):**
```
Size = 1 page = 4 KB
Entries = 1024 (pointing to second-level tables)
```

**Second-Level Page Tables:**
```
Maximum tables = 1024 (one for each first-level entry)
Each table = 4 KB
Maximum total = 1024 × 4 KB = 4 MB
```

**Total Maximum Page Table Memory:**
```
Total = First-level + All second-level tables
      = 4 KB + 4 MB
      ≈ 4 MB
```

**Advantage of Two-Level Paging:**
- Not all second-level tables need to be in memory
- Sparse address spaces save significant memory
- Only allocate page tables for actually used portions of address space

---

## Summary of All Answers

### Exercise 11
- Use cycle detection to identify deadlocks in resource allocation graphs
- A cycle with single-instance resources indicates deadlock
- Prevention methods: Break mutual exclusion, hold-and-wait, no preemption, or circular wait

### Exercise 12

| Part | Question | Answer | Verification |
|------|----------|--------|--------------|
| a | Offset bits in logical address | **12 bits** | log₂(4096) = 12 ✓ |
| b | Bits for page numbers | **20 bits** | 32 - 12 = 20 ✓ |
| d | Number of entries per page table | **1024 entries** | 4096 ÷ 4 = 1024 ✓ |
| e | First-level index bits | **10 bits** | log₂(1024) = 10 ✓ |
| e | Second-level index bits | **10 bits** | log₂(1024) = 10 ✓ |

### Final Cross-Check

```
First-level index (10) + Second-level index (10) + Offset (12) = 32 bits ✓
```

All answers verified correct.
