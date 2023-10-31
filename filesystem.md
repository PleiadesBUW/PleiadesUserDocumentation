---
title: "File Systems"
layout: default
nav_order: 3
has_children: true
---

## File Systems
There are different technologies involved, when dealing with files in our cluster:
  * Node-local disk, e.g. `/tmp` on any login or worker node. Files in here are not shared between nodes, but file interaction is usually pretty fast!
  * Shared parallel file system, e.g. BeeGFS or NFS. This is the location of your `${HOME}` directory and available from any node. BeeGFS is optimized for parallel access of large files. Since network communication is required it will always feel slower than the local disk.
  * CVMFS, a read-only shared file system to distribute software throughout the cluster. The first access can be slow, since the software is downloaded from the internet. Due to caching, every subsequent access is really fast.
