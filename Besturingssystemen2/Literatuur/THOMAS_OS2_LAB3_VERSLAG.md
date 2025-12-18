OS2:LAB3:VERSLAG



Introduction:

SinglThreadedAllocator(STA), MultiThreadedAllocator(MTA), BestFitAllocator(BFA), FirstFitAllocator(FFA), SingleSizedAllocator(SSA)

...



Body:

-Steps

We avoided global synchronization by synchronizing on access to shared resources in our STA.

&nbsp;	-STA

&nbsp;		-BFA

&nbsp;		...

&nbsp;		-FFA

&nbsp;		...

&nbsp;		-SSA

&nbsp;		In this allocator there are two levels in which we make use of shared resources. The top level is the STA itself, the bottom level is the Arena.

&nbsp;			-Top level

&nbsp;			We have four resources which we need to synchronize on. Three resources are maps and the fourht one is the arena. The first one maps base addresses to large allocations, the second one maps block size's to an arena and the third one maps base address of block list to the responsable arena. Whenever a thread tries to access one of these resources, it should first take the lock on it.

&nbsp;			-Bottom level

&nbsp;			Due to synchronizing on our arena we know that it is thread safe and we do not need to synchronize on resources inside it. 

&nbsp;	-MTA

&nbsp;	Our MTA makes use of multiple STA's. Each STA is assigned multiple threads. We already synchronized the our STA, so no need to synchronize inside our MTA.

-Alternatives

At the beginning of this lab we decided to all write our own implementation of a STA. Therefore we have three implementations we can compare with each other.

&nbsp;	-BFA

&nbsp;	...

&nbsp;	-FFA

&nbsp;	...

&nbsp;	-SSA

&nbsp;	...

&nbsp;	-MyAllocatorImpl

&nbsp;	There were two big problems. The first one being the role of the allocator and the second one the synchronization. Due to these two problem, this allocator is performs slow.

&nbsp;		-Role

&nbsp;		For every allocate and free call, the allocator contacts the operating system. This defeats the whole purpose of an allocator. Its job is to manage memory efficient not to pass requests to the OS.		

&nbsp;		-Synchronization

&nbsp;		Every interface method is synchronized with the keyword this. This means that only one thread can have the lock on the allocator, even if two threads want to perform two unrelated actions.

-Optimized

...

&nbsp;	-BFA

&nbsp;	...

&nbsp;	-FFA

&nbsp;	...

&nbsp;	-SSA

&nbsp;	...

-Overhead

...

&nbsp;	-Direct

&nbsp;	?

&nbsp;	-Indirect

&nbsp;	?

-Benchmarks

...



Conclusion:

...



Should contain:

-Steps

-Alternatives

-MyAllocatorImpl

-Optimizes for?

-Direct\&indirect overhead

-Benchmarks (graph)



Questions:

-Compare metrics?

&nbsp;	-internal fragmentation (measure?)

&nbsp;	-external fragmentation (measure?)

&nbsp;	-performance

-What will we benchmark?

-Will we refer to benchmarks (located end report)?

-Possibilities to optimize for?

&nbsp;	-internal f.

&nbsp;	-external f.

&nbsp;	-performances

-What is direct overhead?

-What is indirect overhead?

