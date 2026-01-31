# Operating Systems Exercises - Solutions

## Exercise 11: Deadlock Detection in Resource Allocation Graphs

### Problem Statement
For each of the given resource allocation graphs, determine whether a deadlock can exist or not. If a deadlock exists, explain how it can be prevented.

---

## Graph 1 Analysis (Left Graph)

### Resources and Processes:
- **R1**: 1 instance (single dot)
- **R2**: 2 instances (two dots)
- **R3**: 2 instances (two dots)
- **Processes**: T1, T2, T3, T4

### Current Allocations (Resource → Process):
| Resource | Allocated To | Instances Used |
|----------|--------------|----------------|
| R1 | T1 | 1/1 |
| R2 | T1, T2 | 2/2 |
| R3 | T2, T4 | 2/2 |

### Current Requests (Process → Resource):
| Process | Holds | Requests |
|---------|-------|----------|
| T1 | R1(1), R2(1) | Nothing |
| T2 | R2(1), R3(1) | R1 |
| T3 | Nothing | R1, R2 |
| T4 | R3(1) | R2 |

### Graph Reduction Algorithm:

**Step 1:** Find a process that can complete (all requests satisfiable)
- T1 holds R1 and R2, requests **nothing** → **T1 CAN COMPLETE**

**Step 2:** T1 completes and releases resources
- R1: 1 instance now **FREE**
- R2: 1 instance now **FREE**

**Step 3:** Check remaining processes
- T2 wants R1 → R1 is now available → **T2 CAN COMPLETE**
- T2 releases: R2(1), R3(1)

**Step 4:** After T2 completes
- R1: 1 instance FREE
- R2: 2 instances FREE
- R3: 1 instance FREE

**Step 5:** T3 wants R1 and R2 → Both available → **T3 CAN COMPLETE**

**Step 6:** T4 wants R2 → Available → **T4 CAN COMPLETE**

### Result for Graph 1: ✅ NO DEADLOCK

**Explanation:** The graph can be fully reduced. All processes can eventually complete because T1 is not waiting for any resource and can release R1 and R2, which allows other processes to proceed.

---

## Graph 2 Analysis (Right Graph)

### Resources and Processes:
- **R1**: 2 instances (two dots)
- **R2**: 2 instances (two dots)
- **Processes**: T1, T2, T3

### Current Allocations (Resource → Process):
| Resource | Allocated To | Instances Used | Available |
|----------|--------------|----------------|-----------|
| R1 | T1, T2 | 2/2 | **0** |
| R2 | T2, T3 | 2/2 | **0** |

### Current Requests (Process → Resource):
| Process | Holds | Requests | Status |
|---------|-------|----------|--------|
| T1 | R1(1) | R2 | ❌ BLOCKED (R2 has 0 available) |
| T2 | R1(1), R2(1) | R2 | ❌ BLOCKED (R2 has 0 available) |
| T3 | R2(1) | R1 | ❌ BLOCKED (R1 has 0 available) |

### Graph Reduction Algorithm:

**Step 1:** Find a process that can complete
- T1 wants R2 → 0 available → **BLOCKED**
- T2 wants R2 → 0 available → **BLOCKED**
- T3 wants R1 → 0 available → **BLOCKED**

**All processes are blocked! No process can proceed.**

### Cycle Detection:

```
T1 ──wants──→ R2 ──held by──→ T3 ──wants──→ R1 ──held by──→ T1
     │                                                        ↑
     └────────────────── CYCLE ──────────────────────────────┘
```

**Cycle:** T1 → R2 → T3 → R1 → T1

### Result for Graph 2: ❌ DEADLOCK EXISTS

**Explanation:** 
- T1 holds R1 and waits for R2
- T3 holds R2 and waits for R1
- This creates a circular wait condition
- T2 is also involved, holding both R1 and R2 but waiting for another R2 instance

---

## Deadlock Prevention Strategies for Graph 2

### Method 1: Resource Ordering (Circular Wait Prevention)
- Assign order: R1 < R2
- **Rule:** Processes must request R1 before requesting R2
- T3 violates this rule (holds R2, wants R1)
- **Fix:** T3 should request R1 first, then R2

### Method 2: Hold and Wait Prevention
- Require each process to request ALL needed resources at once
- T1 should request R1 and R2 together atomically
- T3 should request R1 and R2 together atomically

### Method 3: No Preemption
- If T1 cannot get R2, it must release R1
- If T3 cannot get R1, it must release R2
- OS can preempt resources from waiting processes

### Method 4: Deadlock Avoidance (Banker's Algorithm)
- Before allocating, check if the system remains in a safe state
- Deny requests that would lead to unsafe states

---

## Summary for Exercise 11

| Graph | Resources | Processes | Deadlock? | Reason |
|-------|-----------|-----------|-----------|--------|
| Graph 1 | R1(1), R2(2), R3(2) | T1, T2, T3, T4 | **NO** | T1 can complete first, releasing resources |
| Graph 2 | R1(2), R2(2) | T1, T2, T3 | **YES** | Circular wait: T1→R2→T3→R1→T1 |

---

---

## Exercise 12: Two-Level Hierarchical Page Table

### Problem Statement

Consider a memory system with the following specifications:
- **Logical address space**: 32-bit addressing
- **Physical memory size**: 1 GB
- **Page size**: 4 KB
- **Page table entry (PTE) size**: 4 bytes
- **Page table structure**: Two-level hierarchical
- **Constraint**: Each page table fits exactly in one memory page

### Given Information

| Parameter | Value | Binary Representation |
|-----------|-------|----------------------|
| Logical Address Size | 32 bits | - |
| Physical Memory | 1 GB | 2³⁰ bytes |
| Page Size | 4 KB | 2¹² bytes |
| PTE Size | 4 bytes | 2² bytes |

---

### Part (a): Number of Offset Bits in Logical Address

**Solution:**

The offset identifies a specific byte within a page.

```
Offset bits = log₂(Page Size)
Offset bits = log₂(4 KB)
Offset bits = log₂(4 × 1024 bytes)
Offset bits = log₂(4096 bytes)
Offset bits = log₂(2¹²)
Offset bits = 12 bits
```

**Answer: 12 bits**

The offset field uses **12 bits** to address any byte within a 4 KB page (0 to 4095).

---

### Part (b): Bits Remaining for Page Numbers

**Solution:**

The total logical address is 32 bits, and the offset uses 12 bits.

```
Page number bits = Total address bits - Offset bits
Page number bits = 32 - 12
Page number bits = 20 bits
```

**Answer: 20 bits**

There are **20 bits** available for page numbering, which means the system can address:
- 2²⁰ = 1,048,576 pages in the virtual address space

---

### Part (c) and (d): Number of Entries in Each Page Table

**Solution:**

Since each page table must fit exactly in one memory page:

```
Page table size = Page size = 4 KB = 4096 bytes

Number of entries per page table = Page table size / PTE size
Number of entries = 4096 bytes / 4 bytes
Number of entries = 1024 entries
Number of entries = 2¹⁰ entries
```

**Answer: 1024 entries (2¹⁰)**

Each page table (both first-level and second-level) contains exactly **1024 entries**.

---

### Part (e): Index Bits for First-Level and Second-Level

**Solution:**

Each page table has 1024 entries, so we need enough bits to index into 1024 entries:

```
Bits per level = log₂(Number of entries)
Bits per level = log₂(1024)
Bits per level = log₂(2¹⁰)
Bits per level = 10 bits
```

**For the two-level structure:**
- **First-level index (P1)**: 10 bits
- **Second-level index (P2)**: 10 bits
- **Offset (d)**: 12 bits

**Verification:**
```
Total bits = P1 + P2 + Offset
Total bits = 10 + 10 + 12
Total bits = 32 bits ✓
```

**Answer:**
- **First-level index: 10 bits**
- **Second-level index: 10 bits**

---

### Complete Logical Address Format

```
┌─────────────────┬─────────────────┬──────────────────┐
│   P1 (10 bits)  │   P2 (10 bits)  │  Offset (12 bits)│
│   First-Level   │  Second-Level   │   Page Offset    │
│     Index       │     Index       │                  │
├─────────────────┼─────────────────┼──────────────────┤
│  Bits 31-22     │   Bits 21-12    │    Bits 11-0     │
└─────────────────┴─────────────────┴──────────────────┘
         │                 │                  │
         │                 │                  └──► Byte within page
         │                 └──────────────────────► Entry in 2nd level PT
         └────────────────────────────────────────► Entry in 1st level PT (Page Directory)
```

---

### Address Translation Process

1. **Extract P1** (bits 31-22): Use as index into the first-level page table (Page Directory)
2. **Get base address** of second-level page table from the entry
3. **Extract P2** (bits 21-12): Use as index into the second-level page table
4. **Get frame number** from the entry
5. **Extract offset** (bits 11-0): Combine with frame number to form physical address

### Memory Requirements Analysis

**First-Level Page Table (Page Directory):**
- 1 page directory = 1 page = 4 KB
- Contains 1024 entries pointing to second-level tables

**Second-Level Page Tables:**
- Maximum: 1024 second-level tables (one for each first-level entry)
- Each second-level table = 4 KB
- Maximum total for second-level: 1024 × 4 KB = 4 MB

**Total Maximum Page Table Memory:**
```
Total = First-level + All second-level tables
Total = 4 KB + 4 MB
Total = 4 KB + 4096 KB
Total = 4100 KB ≈ 4 MB
```

**Advantage of Two-Level Paging:**
- Not all second-level tables need to be in memory
- Sparse address spaces save significant memory
- Only allocate page tables for actually used portions of address space

---

## Summary of Answers

### Exercise 11
- Use cycle detection for single-instance resources
- Use graph reduction algorithm for multi-instance resources
- Deadlock prevention: Resource ordering (circular wait prevention) is most practical

### Exercise 12

| Question | Answer |
|----------|--------|
| (a) Offset bits | **12 bits** |
| (b) Page number bits | **20 bits** |
| (d) Entries per page table | **1024 entries (2¹⁰)** |
| (e) First-level index bits | **10 bits** |
| (e) Second-level index bits | **10 bits** |

**Logical Address Structure:**
```
| P1 (10 bits) | P2 (10 bits) | Offset (12 bits) |
```
