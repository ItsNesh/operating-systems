# Corrections & Revisions for Weeks 7-11

## Week 7 — Memory-Mapped I/O vs “Everything is a file”

**Revised text**

**Memory-Mapped I/O (MMIO)**  
MMIO maps **device registers** into the CPU’s **physical address space**, so software can use normal loads/stores to talk to the device. This mechanism is **independent** of the Unix idea that “everything is a file.” The *file* abstraction is an OS interface (e.g., `/dev/*` device nodes accessed via `open/read/write`), whereas MMIO is a **hardware access technique** for registers. Use: MMIO for register access; the file interface for user-space I/O APIs.

---

## Week 7 — I/O request lifecycle (soften absolute claim)

**Revised text**

**Lifecycle of an I/O request**  
1) App issues `read()` → 2) OS/driver queues request → 3) Driver programs DMA (or uses PIO) → 4) Device transfers data → 5) Device raises an interrupt; driver completes the request → 6) Kernel wakes the waiting task.

> **Note:** DMA greatly **reduces** CPU busy-waiting, but the CPU may still block while waiting for completion or do other work; it is **not guaranteed** to “never be idle.”

---

## Week 8 — RAID-1 performance (reads/writes)

**Revised text**

**RAID-1 (Mirroring) — Performance notes**

- **Random reads:** Can scale up to roughly **`N · R`** by **load-balancing** reads across mirrors (each read can be served by either copy).  
- **Sequential reads:** With typical implementations that alternate requests across the two copies, throughput approaches **`N · S`** (mirrored pairs can be read in parallel). Some simplistic setups achieve only about **`(N/2) · S`** if they read only one of each mirrored pair — that is a **worst-case**, not inherent.  
- **Writes:** Each logical write must update **both copies**, so aggregate write throughput is approximately that of a **single disk** in each mirrored pair (i.e., no >1× gain), rather than generically “poor.”

---

## Week 8 — RAID-5 random read bandwidth (correct factor)

**Revised text**

**RAID-5 — Random reads**  
Random reads are served **only from data blocks**; parity blocks do not contain user data. Thus practical random-read bandwidth is ~**`(N−1) · R`**, not `N · R`. (Parity helps reconstruction on failure, not read parallelism during normal operation.)

---

## Week 10 — Journaling sequence (ordered/metadata journaling)

**Revised text**

**Ordered (metadata) journaling — correct write order**

A safe high-level sequence is:

1) **Write data blocks to their home locations** (flush).  
2) **Write `TxB` + metadata** (inode, bitmap, directory updates) **to the journal**.  
3) **Flush** the journal metadata.  
4) **Write `TxE` (commit record)** atomically.  
5) **Checkpoint**: later, copy committed metadata to home and advance the journal head.

This preserves the rule “**write the pointed-to objects before pointers**,” so after a crash the journal never replays pointers to unwritten garbage.

---

## Week 11 — LFS inode map (imap) persistence

**Revised text**

**LFS inode map (imap)**  
The imap **must be persisted**: updated imap fragments are written **in the log** alongside other segments. To make recovery fast, the **Checkpoint Region (CR)** periodically records pointers to the newest imap pieces; on crash, the system rebuilds the in-memory imap from the CR + the most recent imap blocks. Keeping a copy in RAM is an optimization, not a statement that the imap “is not on disk.”

---

## Week 11 — LFS directory updates (remove misleading claim)

**Revised text**

**Directory updates in LFS**  
Creating/renaming files **does** write new **directory blocks** (names → inode numbers) into the log; LFS avoids recursive in-place overwrites by **appending** new versions of the **directory, inode, and data** and updating the imap. You *don’t* dodge directory updates; you just **log them sequentially** instead of rewriting in place.

---
