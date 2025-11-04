# Corrections & Revisions (Weeks 4–6)

## Week 6 — Semaphores

### ❌ Original (Final Exam Tips)
> “**When a semaphore’s value is negative, its absolute value equals the number of waiting threads.**” :contentReference[oaicite:0]{index=0}

### ✅ Revised
**POSIX** semaphores (`sem_t`) are defined to be **non-negative**; implementations do **not** expose the number of waiters via a negative value (e.g., `sem_getvalue()` may return `0` even when threads are queued).  
That “negative value equals waiters” invariant applies to **abstract textbook** semaphores (Dijkstra-style), not to the POSIX API you use in C. Keep the conceptual model for proofs, but don’t rely on it in code.

---

## Week 4 — TLBs & Context Switching

### ❌ Original (Context-switch table)
- **ASID (Address Space ID)** — *Pros*: “**No extra comparison needed**”  
- **Multiple TLBs** — “**Dedicated TLB per process or memory type**” :contentReference[oaicite:2]{index=2}

### ✅ Revised
- **ASID (Address Space ID)** — *Pros*: Lets a single TLB safely cache translations from **multiple address spaces** and **avoids full flushes** on context switch. *Note*: Lookups **do** compare both **VPN and ASID** (i.e., **extra tag matching**, not “no extra comparison”).  
- **Multiple TLBs** — In practice, CPUs use **separate iTLB/dTLB** and sometimes **L1/L2 TLBs** for hierarchy/latency—not a “per-process TLB.” Per-process TLBs are **not** how general-purpose CPUs handle context switches; they use **flushes** and/or **ASIDs** instead.

---

## Week 4 — Study Question Wording

### ❌ Original (Key Takeaways / Questions)
> “**How does a dirty bit help optimize TLB management?**”

### ✅ Revised
**Better wording:** “How does a **dirty bit** help optimize **page replacement / write-back** decisions?”  
*Reason*: The dirty (modified) bit primarily informs whether a **page** must be written back to disk on eviction. While TLB entries may mirror a D bit, the optimization target is **paging I/O**, not TLB management itself.

---

## Week 5 — Volatile & Races

### ❌ Original (Study Question)
> “**What would happen if we removed `volatile`… Why is `volatile` necessary here?**” (re: `static volatile int counter = 0;`)
### ✅ Revised
`volatile` **does not** make `counter++` safe in multithreaded code. It only inhibits certain **compiler optimizations** (e.g., caching in a register); it provides **no atomicity or ordering** guarantees between threads. To fix races, use **mutexes** or **atomic operations** (e.g., C11 `atomic_int` with `fetch_add`). Reframe the question to:  
**“Why doesn’t `volatile` fix the race on `counter++`, and what should you use instead?”** :contentReference[oaicite:7]{index=7}

---

## Week 4 — TLB Example (keep, but clarify)
> “**Page size = 4KB; int=4B ⇒ ~1024 ints/page; N=2000 ⇒ 2 pages; miss rate ≈ 2/2000 = 0.1%**”  
This example is **correct**; consider adding: “Assumes sequential access with one compulsory miss per distinct page.”

---

# Drop-in Replacements

> Use these to patch your notes verbatim.

### 1) Week 6 – Final Exam Tips (replace the “negative value” bullet)
- **Understand the invariant (clarified)**: In POSIX, semaphore values are **non-negative**; you **cannot** infer the number of waiters from a negative value. The “negative equals waiters” rule is a **theoretical** model used in proofs, not the POSIX API.

---

### 2) Week 4 – Context Switch Solutions (replace the “ASID / Multiple TLBs” rows)
| Solution | How It Works | Pros | Cons |
|---|---|---|---|
| **Flush the TLB** | Clear entries on context switch | Simple | Cold-start misses for next process |
| **ASID (Address Space ID)** | Tag each TLB entry with **address-space ID** and compare **VPN+ASID** on lookup | Avoids full flushes; allows entries from multiple processes to coexist | Requires hardware support; wider tag compare | 
| **Separate / Hierarchical TLBs** | iTLB vs dTLB; possibly L1/L2 TLBs | Latency/scalability benefits | Not per-process; still need ASIDs or flushes for switches |

---

### 3) Week 4 – Key Takeaways / Questions (replace the “dirty bit” question)
- **How does a dirty bit help optimize page replacement and write-back?** (*Answer you can add later: it avoids unnecessary disk writes when evicting clean pages.*)

---

### 4) Week 5 – Study Question on `volatile` (replace the prompt)
- **Why doesn’t `volatile` make `counter++` thread-safe, and what should you use instead?** (*Expected answer: use mutexes or C11 atomics; `volatile` isn’t a synchronization primitive.*) :contentReference[oaicite:12]{index=12}
