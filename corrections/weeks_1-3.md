## Weeks 1-3 Corrections

#### **1. Address Space Structure**
| Segment | Purpose | Growth Direction | Key Characteristics |
|----------|----------|------------------|----------------------|
| **Code** | Instructions (static, fixed size) | Fixed at top (`0–1KB`) | Packed first; never grows. |
| **Heap** | Dynamically allocated memory via `malloc()` / `new` | **Grows upward** | Starts after code (`1KB`); expands as needed. |
| **Stack** | Local variables, function calls, arguments, return values | **Grows downward** | Starts near top (`16KB`) and expands as functions are called. |

> *Why opposite growth?*  
> To reduce collision probability between heap and stack as they grow toward each other.

---

#### **2. Paging Terminology**
| Term | Definition |
|------|-------------|
| **Page** | Fixed-size block of a process’s *virtual address space*. |
| **Frame** | Fixed-size block in *physical memory* that stores one page. |
| **VPN (Virtual Page Number)** | High-order bits of the virtual address. |
| **PFN (Physical Frame Number)** | Identifies the physical frame containing the page. |

> ✅ Correct terminology: *Virtual memory → pages; physical memory → frames.*

---

#### **3. Multi-Level Paging Clarification**
| Feature | Traditional Page Tables | Multi-Level Page Tables |
|----------|--------------------------|--------------------------|
| **Table Size** | Fixed ~4 MB per process | Grows only as needed. |
| **Memory Usage** | High; wastes entries for unused space | Efficient; allocates only used sub-tables. |
| **Lookup Time** | One memory access | **Multiple memory accesses** (one per level). TLB hides most of this cost. |
| **Fragmentation** | None (fixed pages) | None (fixed pages). |
| **Key Benefit** | Simplicity | **Reduced space usage, not faster lookup.** |

> 💡 *Performance stays acceptable because of the Translation Lookaside Buffer (TLB), which caches recent virtual→physical translations.*

---

#### **4. Canonical 64-bit Addressing**
> Many 64-bit CPUs (e.g., x86-64) **don’t use all 64 bits** for addressing.  
> Typically, 48–57 bits are implemented for *virtual addresses*, with **canonical addressing**: upper bits must match the sign bit (bit 47).  
> This limits virtual address space size while simplifying hardware, not because “no system needs that much RAM.”

---

#### **5. Segmentation Model Clarification**
> The “top-bits segment selector” model (e.g., using two bits to pick Code = 00, Heap = 01, Stack = 10) is a **teaching abstraction**.  
> Real hardware (e.g., x86 segmentation) uses **segment selectors and descriptor tables**, not hardcoded top bits.  
> Modern OSes mostly disable segmentation and rely on paging for memory management.

---

#### **6. Stack Address Translation Rule (Expand-Down Segments)**
> For stacks that grow downward, addresses *below the base* are valid until the stack limit.  
> **Formula:**  
> `Physical = Base − |VirtualOffsetFromStart|`  
> **Example:** Base = 16 KB, offset = −1 KB → Physical = 15 KB.  
> (Removes conflicting earlier formula.)

---

#### **7. Summary of Correct Concepts**
- Heap → grows **upward**; Stack → grows **downward**.  
- Multi-level paging saves **memory**, not time; lookup cost hidden by **TLB**.  
- “Frames” live in physical memory; “pages” live in virtual memory.  
- Real 64-bit systems use **canonical addressing**, not “ignoring bits.”  
- “Top-bits segmentation” is a **pedagogical simplification** only.  
- Stack translation uses a single consistent “expand-down” rule.

---

