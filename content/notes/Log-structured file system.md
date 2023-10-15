---
title: Log-structured file system
date created: 2023-10-15 16.50
tags:
  - linux
---
Log-structured file systems (LFS) are a type of file system used in some operating systems, particularly in the context of SSDs and flash storage. They are designed for improved performance and longevity of these storage devices.

LFS operates like a continuous log of all changes. When new data is written, it's appended to the end of the log. This approach significantly enhances write performance. By appending new data sequentially to the end of the log. 

However, writing data sequentially one by one on a disk can be inefficient due to the rotational latency (in rotational disks) of the disk. When data is written sequentially, the disk may have to reposition itself after each write operation to ensure that the addresses are contiguous, causing delays. For this reason, data is organized into segments, which are fixed-size blocks stored in buffer memory. Each segment is assigned a sequence number. When a segment is full, it is written onto the disk and a new empty one is used to continue writing data.

![[notes/images/lfs/lfs.png]]

Additioanly, in LFS, as data is appended to the log, old data remains in previous segments until it is garbage collected. This means that older versions of files and their metadata can be retained temporarily. Some LFS implementations do provide features for naming and accessing these older versions, effectively offering a form of time-travel or snapshot functionality.

With that, recovery from crashes is simpler. Upon its next mount, the file system does not need to walk all its data structures to fix any inconsistencies, but can reconstruct its state from the last consistent point in the log.

### Garbage Collection

LFS need to manage available space effectively. To avoid the file system from filling up when the log reaches its end and wraps around, LFS clears space at the tail end of the log. This process involves either skipping over data that has newer versions further ahead in the log or moving data to the beginning of the log when no newer versions are present.

To make this space reclamation process more efficient, most LFS implementations don't use purely circular logs. Instead, they divide storage into segments. The head of the log moves into segments that aren't directly next to each other and are already empty. When space is needed, LFS reclaims the segments that have the least amount of data. This approach reduces the work the system has to do and makes it more efficient, but it becomes less effective as the file system fills up and gets closer to full capacity.


### Disadvantages

**Read Performance:** LFS can suffer from slower random read performance compared to traditional file systems. This is because data is scattered across various segments, making it less efficient to retrieve specific pieces of data. While this is less of an issue on SSDs, it can still affect certain workloads.

**Garbage Collection Overhead:** The garbage collection process, which reclaims space by consolidating and cleaning up data, can introduce overhead. It can consume system resources and potentially affect the overall system's performance. Although it may not be applicable in every implementation, there are implementations where it is valid.


### Conclusion

Log-structured file systems provide writes to the disk in a sequential manner and at the same time provide consistency guarantees. Almost its only disadvantage is random reads. Since, reads are heavy in file systems, the model is still under research. Flash disks have very low random read time and hence [flash file systems](https://en.wikipedia.org/wiki/Flash_file_system) based on log-structured file systems are apt for such devices.


### See Also

* https://web.stanford.edu/~ouster/cgi-bin/papers/lfs.pdf
* https://en.wikipedia.org/wiki/Log-structured_file_system#Disadvantages
* https://www.geeksforgeeks.org/log-structured-file-system-lfs/
* https://www.quora.com/What-is-the-difference-between-a-journaling-vs-a-log-structured-file-system
