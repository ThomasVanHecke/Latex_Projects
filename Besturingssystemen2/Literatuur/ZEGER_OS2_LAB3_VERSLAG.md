Steps to avoid global synchronization
 - What is global sync: synhronize on this or synchronized keyword
 - What we did: Used java concurrent dataclasses where possible, if not possible make use of locks on only things that are needed
 - The reason why using global synchronized keywords is bad: even if multithreaded app still performs like a single threaded app but with more overhead

Provided Allocator bad:
 - globaly synchonized
 - All allocations use a fully page -> lots of wasted space
 - All allocations/deallocations go trough the OS

Programming a multi threaded allocator:
 we first created allocators that where fully thread safe and where better designed than the original provided
 - BestFit
  - A dynamic size allocator
  - Optimizations: 
    - when mmap from os release locks.
    - isAccessable is only uses the allocated lists
    - free uses both lists
    - allocate only uses the free list
    - use of java concurrent classes -> less risk of faulty synchronization
  - Design
    - Uses of navigable maps, sets -> need for lookups like floor, ceiling, very fast best case log(n)
    - Use of 2 in sync sets that are both sorted on specific types reduced search times in favor of higher memory overhead -> allocate needs fast access to freeList in sorted by size, free needs fast access to free list sorted by address for merging of blocks
    - reAlloc uses free and allocate -> the logic to get the blocks before and after is already what happens in free. So if we add this logic to reAlloc and we didnt find free blocks before or after we would still need to do the free operation again which would do these lookups again. Therefor we chose to first free and then allocate.
    - Os allocation is page sized alligned -> to minimize time spend asking the OS
    - OS deallocation: only if the merged to be free block is precicly page size aligned and multiple of page size -> reduces deallocation -> allocer keeps "fast" memory direcly availble.
 - Overhead
  - Complex datastructures that take up a lot of space: skiplists, treesets (red, black tree)
  - Computational overhead: treesets since these are semi balanced trees they need to be rebalanced from time to time this can put addional
  - keeping 2 sets in sync  takes a lot of work
  - changing data in these sets is not possible only way to do this is to remove and add back which is slow. -> possible optim: check what has changed address or size and only update the list that is needed.

- MT allocator
 -> 2 approcahes to handle how to know where what is allocated:
  - Add extra metadata to MT above single threaded or don't
 - Extra Meta
  Uses extra structure like hashmap to store addresses to allocator
   -> Downside single object that can be lock contended, extra memory
 - Use a loop to go over all allocators and use the isAllocated function
  -> slow for lots of threads as for loops needs to avg O(n/2) for lookup
   causes unecicery lock concetion of other allocators

 Amount of allocators
  - We don't create 1 allocer for each thread lots of memory overhead
   We only create a set amount of allocators idea -> each physical CPU core gets it own core local allocator. Even only having 16 allocators for 1000 threads is only 62 threads per allocator
   - To have  good peromance it is curcial that the undrelying ST allocator is good -> using provided allocer has abismal performance when using a "multi"-threaded the apporach