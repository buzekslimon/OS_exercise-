# Operating Systems Exercises - Solutions

## Exercise 11: Deadlock Detection in Resource Allocation Graphs

### Problem Statement
For each of the given resource allocation graphs, determine whether a deadlock can exist or not. If a deadlock exists, explain how it can be prevented.

### Solution Approach

#### Understanding Resource Allocation Graphs (RAG)

A Resource Allocation Graph consists of:
- **Process nodes** (circles): Represent processes P1, P2, P3, etc.
- **Resource nodes** (rectangles/squares): Represent resources R1, R2, R3, etc.
- **Request edges** (Process → Resource): A process is requesting a resource
- **Assignment edges** (Resource → Process): A resource is allocated to a process

#### Deadlock Detection Rules

**Rule 1: For Single-Instance Resources**
- A deadlock exists if and only if there is a **cycle** in the resource allocation graph.

**Rule 2: For Multiple-Instance Resources**
- A cycle is a **necessary but not sufficient** condition for deadlock.
- Must use the **cycle detection algorithm** or **banker's algorithm** to verify.

#### Deadlock Detection Algorithm

1. Find all processes that have all their resource requests satisfied (can complete)
2. Release resources held by these processes
3. Repeat until no more processes can complete
4. If any processes remain, they are in deadlock

### Analysis of Common Graph Patterns

#### Pattern 1: Simple Cycle with Single Instances
```
P1 → R1 → P2 → R2 → P1
```
- **Deadlock EXISTS**: P1 holds R2 and waits for R1; P2 holds R1 and waits for R2
- **Prevention**: 
  - Resource ordering (always request R1 before R2)
  - Use preemption (forcibly take resources)
  - Use timeout mechanisms

#### Pattern 2: Cycle with Multiple Instances
```
R1 has 2 instances
P1 holds 1 instance of R1, requests R2
P2 holds R2, requests R1
P3 holds 1 instance of R1
```
- **Deadlock may NOT exist** if P3 can finish and release R1
- Must trace through all possibilities

#### Pattern 3: No Cycle Present
- **No deadlock possible** - safe state guaranteed

### Deadlock Prevention Strategies

1. **Mutual Exclusion Prevention**: Make resources shareable when possible
2. **Hold and Wait Prevention**: Require processes to request all resources at once
3. **No Preemption Prevention**: Allow forcible resource removal
4. **Circular Wait Prevention**: Impose ordering on resource requests

### General Solution Template for Graph Analysis

For each graph in the exercise:

1. **Identify all cycles** by tracing edges
2. **Count resource instances** for each resource type
3. **Apply detection algorithm**:
   - If single instances and cycle exists → **DEADLOCK**
   - If multiple instances, check if cycle can be broken by completing other processes
4. **Propose prevention method**:
   - If deadlock exists: Apply resource ordering or request-all-at-once policy
   - If no deadlock: System is in safe state

---

## Exercise 12: Two-Level Hierarchical Page Table

### Problem Statement

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

### Solution

#### Part (a): Offset Bits in Logical Address

The offset bits determine which byte within a page we're accessing.

**Given:** Page size = 4 KB = 4 × 1024 bytes = 4096 bytes = 2^12 bytes

**Formula:** Number of offset bits = log₂(Page Size)

**Calculation:**
```
Offset bits = log₂(4096) = log₂(2^12) = 12 bits
```

**Answer (a): 12 bits** for the page offset

---

#### Part (b): Bits for Page Numbers

**Given:** Total logical address = 32 bits

**Calculation:**
```
Page number bits = Total address bits - Offset bits
Page number bits = 32 - 12 = 20 bits
```

**Answer (b): 20 bits** for page numbers

---

#### Part (c) & (d): Number of Entries per Page Table

Since each page table must fit exactly in one memory page:

**Given:**
- Page size = 4 KB = 4096 bytes
- Page table entry (PTE) size = 4 bytes

**Calculation:**
```
Number of entries per page table = Page size / PTE size
Number of entries = 4096 bytes / 4 bytes
Number of entries = 1024 entries
Number of entries = 2^10 entries
```

**Answer (d): 1024 entries** (or 2^10 entries) per page table

---

#### Part (e): Bits for First-Level and Second-Level Indices

We need to divide the 20 page number bits between two levels.

**Constraint:** Each page table has 1024 = 2^10 entries

**Therefore:**
- Each index needs **10 bits** to address 1024 entries
- Total page bits needed: 10 + 10 = 20 bits ✓ (matches our calculation in part b)

**Logical Address Structure (32 bits total):**

```
┌─────────────────┬─────────────────┬─────────────────┐
│  First-Level    │  Second-Level   │     Offset      │
│    Index        │     Index       │                 │
├─────────────────┼─────────────────┼─────────────────┤
│    10 bits      │    10 bits      │    12 bits      │
└─────────────────┴─────────────────┴─────────────────┘
        ↓                 ↓                 ↓
   Bits 31-22       Bits 21-12         Bits 11-0
```

**Answer (e):**
- **First-level (outer) page index: 10 bits**
- **Second-level (inner) page index: 10 bits**

---

### Complete Summary of Exercise 12

| Parameter | Value |
|-----------|-------|
| Total logical address bits | 32 bits |
| Page size | 4 KB (4096 bytes) |
| **Offset bits** | **12 bits** |
| **Page number bits** | **20 bits** |
| PTE size | 4 bytes |
| **Entries per page table** | **1024 (2^10)** |
| **First-level index bits** | **10 bits** |
| **Second-level index bits** | **10 bits** |

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
