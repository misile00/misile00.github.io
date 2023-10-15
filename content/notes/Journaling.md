---
title: Journaling
date created: 2023-10-14 20.35
tags:
  - linux
---
Journaling is a feature used by many modern file systems to improve data integrity and recovery in case of system crashes or unexpected power outages. It maintains a record of changes made to the file system, making it easier to recover from failures without extensive file system checks.

## Journaling techniques

In file systems, there are several different techniques for journaling. These techniques have their own advantages and disadvantages, and some file systems can support multiple of these techniques simultaneously. However, these techniques can be grouped into two primary categories. These are as follows:

### Metadata journaling - Logical journalling

This technique focuses on preserving the metadata, which includes essential inode information, in the log. This process typically involves three key steps: writing a block from memory to disk, updating the corresponding inode in the log, and then copying the modified inode from the log back to the disk. The primary advantage of this approach is that it guarantees the consistency of metadata, helping prevent inconsistencies between inode block pointers and the actual data blocks on disk. Moreover, it offers the benefit of faster checks compared to traditional file system consistency checks (fsck). However, it's important to note that metadata journaling doesn't ensure complete data consistency, and it may lead to redundancy in metadata writes due to the logging process. 

### Physical journaling

Physical journaling is a journaling technique that focuses on preserving complete data consistency by copying the file data, in terms of blocks, into the journal. In this approach, once a transaction is confirmed, the logs are marked as committed and subsequently written to the disk. The most significant advantage of physical journaling is the assurance of complete data consistency, which is crucial for maintaining critical data integrity. However, this method has a considerable impact on system performance due to the additional writes required in the journaling process. As a result, it is generally used in database systems and servers where data is genuinely critical.

> [!note] Notes
> Journaling can be disabled in some file systems, but many file systems do not support turning off journaling, but they do support configuring journaling. 
> 
> Journaling can be turned off in ext3/ext4 with this command:
>
> ```
> tune2fs -O ^has_journal /dev/sdXY

Especially in embedded systems where resources are limited, disabling journaling may be desired to reduce performance and storage requirements, but it is generally not recommended due to the increased risk of data loss and instability. Instead, optimizing journaling settings or researching alternative file systems that best match specific use cases and storage requirements is a preferable approach. 

## See Also

* https://wiki.archlinux.org/title/file_systems#Journaling
* https://en.wikipedia.org/wiki/Journaling_file_system
* https://www.geeksforgeeks.org/journaling-or-write-ahead-logging/
* [[notes/Copy-on-Write]]