### Wear‑leveling and data‑loss risk on SSDs after file deletion

* **What happens when a file is deleted**  
  - On both spinning disks and flash‑based storage the operating system simply marks the file’s directory entry as deleted and clears the logical references to the blocks that contained the file’s data.  
  - The *physical* bytes that made up the file are **not** wiped immediately. They stay on the flash cells until a later erase cycle actually clears them. This is why, in the transcript from Source 2, the lecturer notes that “a file’s data is _not_ physically erased when it is deleted” [Source 2].

* **Role of wear‑leveling on an SSD**  
  - SSD controllers keep track of how many program/erase cycles each physical block has endured. Because flash cells wear out after a limited number of writes, the controller’s *wear‑leveling* algorithm moves data around so that all blocks receive a roughly equal amount of wear.  
  - The algorithm will relocate a block that was written a long time ago (even a deleted file) to a new physical location if that block would otherwise become a write hotspot.  
  - Therefore a deleted file’s bits can be moved out of their original location and eventually overwritten by new data earlier than they would on a magnetic disk, where the data simply stays in place until the logical file system marks it as free.  
  - In practice this means that the “invisible” data of a deleted file has a shorter effective lifetime on an SSD than on a hard‑disk drive, and it can be lost before a user recovers it from a backup or a “deleted‑file recovery” tool.

* **Risk mitigation**  
  - File‑system level “secure delete” utilities may issue a sequence of write‑over commands (e.g., overwrite with zeros or random bytes) that force the SSD controller to relocate and then erase the data.  
  - However, these utilities usually operate at the block level, not the logical‑file level, and they still rely on the wear‑leveling mechanism to distribute those writes, which can inadvertently accelerate the deletion of data that the user expected to survive.  
  - Users wishing to preserve data beyond the life of the drive should copy it to a backup medium that is not subject to wear‑leveling, such as a magnetic disk or a separate encrypted archive.

---

### Concrete RAID capacity and parity‑overhead calculations

The transcript from Source 5 gives a **specific numeric example** that clarifies how parity reduces usable space in a RAID 5 array:

> “A 4‑drive array with 1 GB drives yields 3 GB of usable space, but 3 GB is consumed by parity data in RAID 5.” [Source 5]

The markdown notes for the lecture only list the generic formulas for RAID 0, RAID 1, and RAID 5, but do not provide this illustrative calculation. Below is a minimal table that shows the arithmetic for the example discussed in the lecture:

| RAID configuration | Total raw capacity | Usable capacity | Parity overhead |
| --- | --- | --- | --- |
| 4 × 1 GB (RAID 5) | 4 GB | 3 GB | 1 GB (25 %) |

**Explanation of the numbers**  
- **Raw capacity** is the sum of all disk sizes (4 GB).  
- In RAID 5, a single parity block is distributed across all drives. The parity occupies the space of one full drive, so the usable space equals *(total – parity)* = 4 GB – 1 GB = 3 GB.  
- The parity overhead is therefore 25 % of the raw capacity, which is a key design consideration when selecting RAID level for capacity‑constrained workloads.

---

### The fact that a “.txt” file actually stores raw bits rather than “text”

The transcript from Source 2 emphasizes that a plain‑text file is nothing more than a sequence of bits:

> “The file is just a sequence of bits; it is the software layer that interprets those bits as characters.” [Source 2]

File systems do not understand the semantic meaning of a file’s extension. The “.txt” extension is a convention that most editors and utilities use to decide how to display the contents, but the underlying storage is a contiguous bitstream.  

**Why this matters**  
- **Security & privacy**: Because the bits remain physically present until overwritten, forensic investigators can recover deleted text files from a disk or SSD if they know the file’s original length and position, even though the OS shows it as deleted.  
- **Storage behavior**: Flash memory deals only with bit patterns, so the file system’s notion of “text” has no influence on how an SSD controller writes or erases data.  
- **Compatibility**: A “.txt” file containing binary data (e.g., a compiled executable) will still have the “.txt” extension, but no text editor will display it correctly unless it is interpreted as raw binary.  

Understanding that a file is a raw bit sequence clarifies many of the quirks discussed elsewhere in the course, such as the necessity for wear‑leveling on flash, the cost of parity blocks in RAID, and the challenges of reliable data recovery after deletion.
