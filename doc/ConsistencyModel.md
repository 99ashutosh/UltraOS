## Memory Consistency Model

RISC-V adopts a weak consistency model, which means that the CPU can run programs with higher performance, and the processing of data consistency is handed over to the operating system. Therefore, we need to make good use of the corresponding mechanism when optimizing to improve our performance. Strictly speaking, this is not just a performance problem. In multi-core support, non-standard use will cause more serious functional problems.

However, UltraOS does not know enough about the memory consistency model of RISC-V, so there may be some mistakes in the following content, please correct me.

### Overview

UltraOS needs to solve these important data components: ICache, DCache, TLB and Memory.

For a single core, the internal data of ICache and DCache are not synchronized, which means that the data of the code segment is modified, and ICache needs to be explicitly notified to update. DCache and Memory are obviously not synchronized, and explicit notification is also required. TLB and Memory are asynchronous, and DCache is also asynchronous. But the operating system knows when it is out of sync, so it needs to explicitly use some instructions to do manual sync.

At the same time, it should be noted that we believe that the data consistency between multi-core DCaches should be guaranteed, that is to say, the data between each DCache is synchronized. But the memory is not synchronized with them, the TLB can't see it, and the respective ICache can't see it.

Next, we will briefly introduce (the time for document writing is not enough) the function of the relevant instructions, how the instructions should be used, and how our current design needs to be adapted and implemented.

### Related Instructions

In fact, there are only these three instructions on the k210: `sfence.vm`, `fence`, `fence.i`. 

According to our understanding, the effect is as follows:

- `sfence.vm`: Flush the TLB. This passively synchronizes the TLB with memory.
- `fence`: Refresh the DCache and actively synchronize the data in it to the memory. In this way, relevant data can be seen globally.
- `fence.i`: Refreshes ICache and instruction fetch front-end, passively synchronizes ICache, DCache and memory. Simply put, the ICache written by this core can now be seen. But this can only guarantee that the core is like this. If other cores change the instruction code, then they have to synchronize across cores. It seems that RISC-V mentioned that EEI is related to this situation. I don’t know if this is the case. What is it.

The `sfence.vma` command is a new version of the command, replacing `sfence.vm`. 

It seems that `TLB` flushing can be performed for a certain address, which is more efficient. Other differences are not well understood at the moment.


### When to use related commands

According to the above characteristics, we need to roughly summarize the timing of using the corresponding instructions.

- TLB Shootdown: When the global page table is modified, all cores need to be synchronized and used, and all TLBs need to be refreshed. At this time, we need to use fence for the current process to synchronize the data into memory, and then ecall into sbi to use IPI notification All cores use sfence to flush the TLB.
- Modify process page table entries: There are actually three operations for modification: adding, deleting, and modifying permissions.
  - Add: Fence should be used to synchronize the added page table entries into memory.
  - Deleted: Do not use fence synchronization, but also refresh TLB.
  - Modification: same as deletion.
- Replace the page table: just synchronize the TLB, sfence.
- Modify code: Synchronize ICache, fence.i. In the case of multi-core, a fence is required (all core ICaches can see the code modification, and the corresponding changes can be detected when the process is switched), and then fence.i.


### When UltraOS Uses Related Instructions

- TLB Shootdown will not appear in our design, and no corresponding operation is required.
- When the page table is replaced, it will only be when the process is scheduled. At this time, we will refresh the TLB.
- exec will modify the code, need `fence` and then `fence.i`
- `lazy` and `CoW` as well as mmap and mprotect will modify the page table, so it is necessary to fence+refresh the TLB after the operation (this type of operation is the most common).
