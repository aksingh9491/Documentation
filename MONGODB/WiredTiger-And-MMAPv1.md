#### Difference Between WiredTiger And MMAPv1 :

A database storage engine is the underlying software that a DBMS uses to create, read, update and delete data from a database. The storage engine should be thought of as a “bolt on” to the database (server daemon), which controls the database’s interaction with memory and storage subsystems. Thus, the storage engine is not actually the database, but a service that the database consumes for the storage and retrieval of information. Given that the storage engine is responsible for managing the information stored in the database, it greatly affects the overall performance of the database (or lack thereof, if the wrong engine is chosen).

MMAPv1 is MongoDB’s original storage engine, and was the default engine in MongoDB 3.0 and earlier. It is a B-tree based system that offloads much of the functions of storage interaction and memory management to the operating system. MongoDB is based on memory mapped files.

The MMAP storage engine uses a process called “record allocation” to grab disk space for document storage. All records are contiguously located on disk, and when a document becomes larger than the allocated record, it must allocate a new record. New allocations require moving a document and updating all indexes that refer to the document, which takes more time than in-place updates and leads to storage fragmentation. Furthermore, MMAPv1 in it’s current iterations usually leads to high space utilization on your filesystem due to over-allocation of record space and it’s lack of support for compression.

As mentioned previously, a storage engine’s locking scheme is one of the most important factors in overall database performance. MMAPv1 has collection-level locking – meaning only one insert, update or delete operation can use a collection at a time. This type of locking scheme creates a very common scenario in concurrent workloads, where update/delete/insert operations are always waiting for the operation(s) in front of them to complete. Furthermore, oftentimes those operations are flowing in more quickly than they can be completed in serial fashion by the storage engine. To put it in context, imagine a giant supermarket on Sunday afternoon that only has one checkout line open: plenty of customers, but low throughput!

Given the storage engine choices brought about by the storage engine API in MongoDB 3.0, it is hard to imagine an application that demands the MMAPv1 storage engine for optimized performance. If you read between the lines, you could conclude that MongoDB, Inc. would agree given that the default engine was switched to WiredTiger in v3.2 .