# Operating Systems Exercises - Solutions

**All answers have been double-checked and verified**

---

## Exercise 11: Deadlock Detection in Resource Allocation Graphs

### Problem Statement (Translated from Persian)
For each of the given resource allocation graphs, determine whether a deadlock can exist or not. If a deadlock exists, explain how it can be prevented.

---

### Graph 1 Analysis (Left Graph) ✅

**Resources and Instances:**
- R1: 1 instance (●)
- R2: 2 instances (●●)
- R3: 2 instances (●●)

**Processes:** T1, T2, T3, T4

**Allocations (Resource → Process):**
- R1 → T2 (T2 holds the only instance of R1)
- R2 → T3 (T3 holds 1 instance of R2)
- R2 → T4 (T4 holds 1 instance of R2)
- R3 → T1 (T1 holds 1 instance of R3)
- R3 → T2 (T2 holds 1 instance of R3)

**Requests (Process → Resource):**
- T1 → R1 (T1 requests R1)
- T2 → R2 (T2 requests R2)
- T3 → R3 (T3 requests R3)
- T4 → R3 (T4 requests R3)

**Current State Summary:**
| Process | Holds | Requests |
|---------|-------|----------|
| T1 | 1× R3 | R1 |
| T2 | 1× R1, 1× R3 | R2 |
| T3 | 1× R2 | R3 |
| T4 | 1× R2 | R3 |

**Deadlock Detection:**

Step 1: Check available resources
- R1: 1 total - 1 allocated = 0 available
- R2: 2 total - 2 allocated = 0 available
- R3: 2 total - 2 allocated = 0 available

Step 2: Can any process complete?
- T1 needs R1 → 0 available → **BLOCKED**
- T2 needs R2 → 0 available → **BLOCKED**
- T3 needs R3 → 0 available → **BLOCKED**
- T4 needs R3 → 0 available → **BLOCKED**

Step 3: Identify cycles
```
Cycle 1: T1 → R1 → T2 → R2 → T3 → R3 → T1
Cycle 2: T1 → R1 → T2 → R2 → T4 → R3 → T1
```

**✅ RESULT: DEADLOCK EXISTS**

All processes are blocked in a circular wait. No process can proceed.

**Prevention Methods for Graph 1:**
1. **Resource Ordering:** Require processes to request resources in order (R1 → R2 → R3)
2. **Hold and Wait Prevention:** T2 should request R1 and R2 together at the start
3. **Preemption:** Allow the OS to forcibly take R3 from T1 or T2
4. **Banker's Algorithm:** Use safe state checking before granting resources

---

### Graph 2 Analysis (Right Graph) ✅

**Resources and Instances:**
- R1: 2 instances (●●)
- R2: 2 instances (●●)

**Processes:** T1, T2, T3

**Allocations (Resource → Process):**
- R1 → T1 (T1 holds 1 instance of R1)
- R1 → T2 (T2 holds 1 instance of R1)
- R2 → T2 (T2 holds 1 instance of R2)
- R2 → T3 (T3 holds 1 instance of R2)

**Requests (Process → Resource):**
- T1 → R2 (T1 requests R2)
- T2 → R2 (T2 requests R2 - needs another instance)
- T3 → R1 (T3 requests R1)

**Current State Summary:**
| Process | Holds | Requests |
|---------|-------|----------|
| T1 | 1× R1 | R2 |
| T2 | 1× R1, 1× R2 | R2 |
| T3 | 1× R2 | R1 |

**Deadlock Detection:**

Step 1: Check available resources
- R1: 2 total - 2 allocated = 0 available
- R2: 2 total - 2 allocated = 0 available

Step 2: Can any process complete?
- T1 needs R2 → 0 available → **BLOCKED**
- T2 needs R2 → 0 available → **BLOCKED**
- T3 needs R1 → 0 available → **BLOCKED**

Step 3: Identify cycles
```
Cycle: T1 → R2 → T3 → R1 → T1
Also:  T2 → R2 → T3 → R1 → T2
```

**✅ RESULT: DEADLOCK EXISTS**

All three processes are blocked. None can release resources because each is waiting for a resource held by another.

**Prevention Methods for Graph 2:**
1. **Resource Ordering:** Always request R1 before R2
2. **Hold and Wait Prevention:** Each process should request all needed resources at once
3. **Limit Resource Holding:** T2 should not hold both R1 and R2 simultaneously while requesting more
4. **Timeout Mechanism:** Release resources if waiting too long

---

### Summary of Exercise 11

| Graph | Deadlock? | Reason |
|-------|-----------|--------|
| **Graph 1** | **YES** | Circular wait: T1→R1→T2→R2→T3→R3→T1 |
| **Graph 2** | **YES** | Circular wait: T1→R2→T3→R1→T1 |

### General Deadlock Prevention Strategies

1. **Mutual Exclusion Prevention**: Make resources shareable when possible
2. **Hold and Wait Prevention**: Require processes to request all resources at once
3. **No Preemption Prevention**: Allow forcible resource removal
4. **Circular Wait Prevention**: Impose ordering on resource requests (most practical)

---

## Exercise 12: Two-Level Hierarchical Page Table

### Problem Statement (Translated from Persian)

Consider a memory system with:
- **Address space**: 32-bit addressing
- **Physical memory size**: 1 GB
- **Page size**: 4 KB
- **Page table entry size**: 4 bytes
- **Structure**: Two-level hierarchical page table
- **Constraint**: Each page table fits exactly in one memory page

Calculate:
- a) Number of offset bits in the logical address
- b) Number of bits remaining for page numbers
- c) Given each page table fits in one page:
- d) Number of entries in each page table
- e) Number of bits for first-level and second-level page indices

---

### Solution (Double-Checked and Verified)

#### Part (a): Offset Bits in Logical Address ✅ VERIFIED

The offset bits determine which byte within a page we're accessing.

**Given:** Page size = 4 KB

**Step-by-step calculation:**
```
Page size = 4 KB
         = 4 × 1024 bytes
         = 4096 bytes
         = 2^12 bytes

Offset bits = log₂(Page Size)
            = log₂(4096)
            = log₂(2^12)
            = 12 bits
```

**✅ Answer (a): 12 bits** for the page offset

---

#### Part (b): Bits for Page Numbers ✅ VERIFIED

**Given:** Total logical address = 32 bits

**Step-by-step calculation:**
```
Page number bits = Total address bits - Offset bits
                 = 32 bits - 12 bits
                 = 20 bits
```

**✅ Answer (b): 20 bits** for page numbers

---

#### Part (c) & (d): Number of Entries per Page Table ✅ VERIFIED

Since each page table must fit exactly in one memory page:

**Given:**
- Page size = 4 KB = 4096 bytes
- Page table entry (PTE) size = 4 bytes

**Step-by-step calculation:**
```
Number of entries per page table = Page size / PTE size
                                 = 4096 bytes / 4 bytes per entry
                                 = 1024 entries
                                 = 2^10 entries
```

**✅ Answer (d): 1024 entries** (or 2^10 entries) per page table

---

#### Part (e): Bits for First-Level and Second-Level Indices ✅ VERIFIED

We need to divide the 20 page number bits between two levels.

**Step-by-step calculation:**
```
Each page table has 1024 entries = 2^10 entries

Bits needed to index one page table = log₂(1024)
                                    = log₂(2^10)
                                    = 10 bits

First-level index bits  = 10 bits (to select from 1024 entries)
Second-level index bits = 10 bits (to select from 1024 entries)

Verification: 10 + 10 = 20 bits ✓ (matches page number bits from part b)
```

**Logical Address Structure (32 bits total):**

```
┌─────────────────┬─────────────────┬─────────────────┐
│  First-Level    │  Second-Level   │     Offset      │
│    Index (P1)   │    Index (P2)   │      (d)        │
├─────────────────┼─────────────────┼─────────────────┤
│    10 bits      │    10 bits      │    12 bits      │
└─────────────────┴─────────────────┴─────────────────┘
        ↓                 ↓                 ↓
   Bits 31-22       Bits 21-12         Bits 11-0
```

**✅ Answer (e):**
- **First-level (outer) page index: 10 bits**
- **Second-level (inner) page index: 10 bits**

---

### Complete Summary of Exercise 12

| Parameter | Value | Verification |
|-----------|-------|--------------|
| Total logical address bits | 32 bits | Given |
| Page size | 4 KB (4096 bytes) | Given |
| **Offset bits** | **12 bits** | log₂(4096) = 12 ✅ |
| **Page number bits** | **20 bits** | 32 - 12 = 20 ✅ |
| PTE size | 4 bytes | Given |
| **Entries per page table** | **1024 (2^10)** | 4096/4 = 1024 ✅ |
| **First-level index bits** | **10 bits** | log₂(1024) = 10 ✅ |
| **Second-level index bits** | **10 bits** | log₂(1024) = 10 ✅ |

### Cross-Verification Check

```
First-level bits + Second-level bits + Offset bits = Total address bits
      10         +        10         +     12      =      32 ✅ CORRECT
```

### Address Translation Process

1. **Extract** the 10 most significant bits (bits 31-22) as the first-level index
2. **Index** into the outer page table to get the base address of the inner page table
3. **Extract** the next 10 bits (bits 21-12) as the second-level index
4. **Index** into the inner page table to get the physical frame number
5. **Combine** the frame number with the 12-bit offset (bits 11-0) to get the physical address

### Additional Calculations (for reference)

**Total number of pages in virtual address space:**
```
Virtual pages = 2^20 = 1,048,576 pages
```

**Physical memory frames:**
```
Physical memory = 1 GB = 2^30 bytes
Frame size = 4 KB = 2^12 bytes
Number of frames = 2^30 / 2^12 = 2^18 = 262,144 frames
```

**Bits needed for physical frame number:**
```
Frame number bits = log₂(262,144) = 18 bits
```

**Page table entry content:**
- Frame number: 18 bits
- Remaining 14 bits (4 bytes × 8 - 18 = 14): Used for flags (valid bit, dirty bit, protection bits, etc.)

---

## Key Concepts Summary

### Deadlock (Exercise 11)
- Deadlock occurs when processes are waiting circularly for resources
- Detection: Look for cycles in resource allocation graphs
- Prevention: Resource ordering, hold-and-wait elimination, preemption, or circular wait prevention

### Paging (Exercise 12)
- Two-level paging reduces page table memory overhead
- Offset bits = log₂(page size)
- Page table entries per table = page size / entry size
- Index bits at each level = log₂(entries per table)
