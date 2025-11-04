# Weeks 1-3 Corrections

### **Address Space Structure**
| Segment | Purpose | Growth Direction | Key Characteristics |
|----------|----------|------------------|----------------------|
| **Code** | Instructions (static, fixed size) | Fixed at top (`0–1KB`) | Packed first; never grows. |
| **Heap** | Dynamically allocated memory via `malloc()` / `new` | **Grows upward** | Starts after code (`1KB`); expands as needed. |
| **Stack** | Local variables, function calls, arguments, return values | **Grows downward** | Starts near top (`16KB`) and expands as functions are called. |

> *Why opposite growth?*  
> To reduce collision probability between heap and stack as they grow toward each other.

---

### **Paging Terminology**
| Term | Definition |
|------|-------------|
| **Page** | Fixed-size block of a process’s *virtual address space*. |
| **Frame** | Fixed-size block in *physical memory* that stores one page. |
| **VPN (Virtual Page Number)** | High-order bits of the virtual address. |
| **PFN (Physical Frame Number)** | Identifies the physical frame containing the page. |

> ✅ Correct terminology: *Virtual memory → pages; physical memory → frames.*

---

## Week 1 — Process vs. Thread (Fix shared PC/SP/registers)

### Process vs. Thread: The Critical Difference
| **Process** | **Thread** |
|---|---|
| Has its **own address space** (separate virtual memory) | **Shares the same address space** with other threads of the *same* process |
| **Has its own** registers, **program counter (PC)**, and **stack** | **Has its own** registers, **PC**, and **stack** (one stack per thread) |
| Cannot directly access another process’s memory | Communicates via shared memory (since address space is shared) |

> **Why this change:** The earlier table said a thread *“Shares program counter, stack pointer, and registers”*, which is incorrect—threads share the **address space** (and other per-process resources) but **each thread has its own PC, SP, and register set**.

---

## Week 1 — Memory Layout (Add the missing heap and fix ordering)

### Process Memory Structure (Revised)
Each process typically has:
- **Code (text)**
- **Data** (initialized + BSS)
- **Heap** (grows upward)
- **Stack** (grows downward)

Typical Process Layout (conceptual):

```
+------------------+   high addresses
|      Stack       |   ↓ grows down
+------------------+
|       ...        |
+------------------+
|       Heap       |   ↑ grows up
+------------------+
|  Data (BSS/Data) |
+------------------+
|      Code        |
+------------------+   low addresses
```

> **Why this change:** The original diagram omitted the **heap** and ordered sections as “Data, Code, Stack,” which can mislead learners about the standard conceptual layout.

---

## Week 1 — Registers table example (Fix invalid assembly syntax)

### Registers and Their Roles (example instruction corrected)
| **Register** | **Purpose** | **Example Instruction** |
|---|---|---|
| Program Counter (PC) | Current instruction location | *(architecture-defined; not set directly in user code)* |
| Stack Pointer (SP) | Location of top of stack | `push %eax`, `pop %eax` (AT&T) |
| Accumulator | Temporary storage for calculations | `mov $0xABC123, %eax` (AT&T) / `mov eax, 0xABC123` (Intel) |
| Status Register | Tracks system state (flags, interrupts) | Updates via arithmetic/logic ops |

> **Why this change:** The previous example `movl %0xabc123, eax` mixed syntaxes and used an invalid immediate form; the corrected examples show valid AT&T and Intel variants.

---

## Week 2 — Round Robin (q = 2) wording (Clarify “finishes after 5 quanta”)

### Round Robin (Quantum = 2, No Context Switch Cost)
- **Execution order** (q=2) remains the same as in the notes.  
- **Completions**: `A` at **t=24**, `B` at **t=28**, `C` at **t=30** (these are **clock times**).  
- **CPU time consumed** by each job is still its **burst** (10 ms each).  
- **Response times**: `A=0`, `B=0` (first runs at arrival `t=2`), `C=2` (first run at `t=6`).  
- **Turnaround** = `Completion − Arrival` → `A=24`, `B=26`, `C=26`.

> **Why this change:** Replaces the ambiguous line “A finishes after 5 quanta (**24 ms total**)” with explicit **completion times** vs **CPU time used** to avoid conflating the two.

---

## Week 3 — Base/Bounds Wastefulness (Remove “all 4 GB must be resident” overstatement)

### The Wastefulness of Single-Base Relocation (Revised)
- With a **single base/bounds pair**, a process must occupy **one contiguous physical region** large enough to cover **from its lowest to highest used virtual address**.
- If large unused gaps exist between regions (e.g., between stack and heap), that **hole** is included in the contiguous allocation, **wasting RAM**.
- This does **not** mean “all 4 GB” of a 32-bit virtual space must be resident; the waste arises because a **single segment cannot represent holes** in a sparse address space.

> **Why this change:** The original text said “all virtual address space must reside in physical memory,” which is stronger than necessary and inaccurate in general. The key problem is **contiguity**, not “everything must be loaded.” :contentReference[oaicite:4]{index=4}

---

## Week 3 — Stack translation (Keep one correct formula; drop confusing intermediate)

### Handling the Stack’s Negative Growth — Example (Cleaned Up)
- **Stack grows downward** (toward **lower** virtual addresses).
- Stack segment: `Base = 28 KB` (physical), `Size = 2 KB`, virtual **start** at `16 KB`.
- For virtual address **15 360** (i.e., **15 KB**):
  - **Offset in virtual space** = `15 360 − 16 384 = −1 024`.
  - **Physical address** = `Base + (−offset)` = `28 KB + 1 024` = **27 KB**.
  - Bounds check: `|−1 024| ≤ 2 KB` → **valid**.

> **Why this change:** Removes the contradictory intermediate “Segment_Max_Size − …” path and retains the direct, correct computation used later in the notes.

