# Operating Systems Exercises - Solutions

## Exercise 11: Deadlock Detection in Resource Allocation Graphs

### Problem Statement
For each of the given resource allocation graphs, determine whether a deadlock can exist or not. If a deadlock exists, explain how it can be prevented.

### Solution Methodology

#### Understanding Resource Allocation Graphs (RAG)

A Resource Allocation Graph consists of:
- **Process nodes** (circles): Represent processes (P1, P2, P3, ...)
- **Resource nodes** (rectangles): Represent resources (R1, R2, R3, ...)
- **Request edges** (Process → Resource): Process is requesting a resource
- **Assignment edges** (Resource → Process): Resource is allocated to a process

#### Deadlock Detection Rules

**For Single-Instance Resources:**
- A deadlock exists **if and only if** there is a **cycle** in the graph.

**For Multi-Instance Resources:**
- A cycle is a **necessary but not sufficient** condition for deadlock.
- Must use the **graph reduction algorithm**:
  1. Find a process whose resource requests can all be satisfied
  2. Remove all edges to/from that process (simulate completion)
  3. Repeat until no more processes can be reduced
  4. If any processes remain, a deadlock exists

#### General Analysis Pattern

**Graph Analysis Steps:**
1. Identify all processes and resources
2. Trace all request and assignment edges
3. Look for cycles in the graph
4. For multi-instance resources, apply the reduction algorithm

#### Common Deadlock Scenarios

**Scenario A: Simple Cycle (Deadlock EXISTS)**
```
P1 → R1 → P2 → R2 → P1
```
- P1 holds R2, wants R1
- P2 holds R1, wants R2
- **Result: DEADLOCK** - circular wait condition

**Scenario B: Cycle with Available Resources (NO Deadlock)**
```
P1 → R1 (has 2 instances, 1 allocated to P2, 1 free)
P2 → R2 → P1
```
- Even though there's a cycle, P1 can get R1 (free instance available)
- After P1 finishes, R2 is released for P2
- **Result: NO DEADLOCK**

**Scenario C: Multiple Cycles (Deadlock EXISTS)**
```
P1 → R1 → P2 → R2 → P3 → R3 → P1
```
- Circular chain of waiting
- **Result: DEADLOCK**

### Deadlock Prevention Strategies

If a deadlock exists, it can be prevented using one of these methods:

#### 1. **Mutual Exclusion Prevention**
- Make resources shareable where possible
- Not always feasible (e.g., printers, files being written)

#### 2. **Hold and Wait Prevention**
- Require processes to request all resources at once before execution
- Or require a process to release all resources before requesting new ones
- **Implementation**: Process must declare all needed resources upfront

#### 3. **No Preemption Prevention**
- If a process holding resources requests another that cannot be allocated:
  - Release all currently held resources
  - Process restarts and re-requests all resources
- **Implementation**: Allow OS to preempt resources from waiting processes

#### 4. **Circular Wait Prevention** (Most Practical)
- Impose a total ordering on all resource types
- Require processes to request resources in increasing order
- **Implementation**: Number all resources R1 < R2 < R3 < ...
  - A process holding Ri can only request Rj where j > i

### Example Solutions for Typical Graph Patterns

**Pattern 1: Two processes, two resources (Classic Deadlock)**
```
    R1          R2
    ↓           ↓
    P1 ←───── P2
    │           ↑
    └───────────┘
```
- **Analysis**: P1 holds R1, wants R2. P2 holds R2, wants R1.
- **Verdict**: DEADLOCK EXISTS
- **Prevention**: 
  - Require both processes to request R1 before R2 (resource ordering)
  - Or require processes to request both resources atomically

**Pattern 2: Three processes, three resources (Chain)**
```
P1 → R1 → P2 → R2 → P3 → R3 → P1
```
- **Analysis**: Circular chain of dependencies
- **Verdict**: DEADLOCK EXISTS
- **Prevention**: Break the circular wait by enforcing resource ordering

**Pattern 3: Fork structure (No Deadlock)**
```
    P1 → R1 ← P2
         ↓
        P3
```
- **Analysis**: No cycle, just competition for R1
- **Verdict**: NO DEADLOCK (one process will wait, but eventually get R1)

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
