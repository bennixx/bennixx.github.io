---
layout: post
title: Memory Management System Redesign
---

I won't lie, memory management is _the most_ frustrating thing in OS design for me. It takes the most time and debugging it is nevertheless exhausting. I have, however, redesigned it to be more generic than before and relax much more arch. dependent code.

## The New Design

### Physical Memory Management
EasyOS uses zoned buddy memory allocation technique, which is used in Linux and many \*BSDs. Currently we only have two zones `ZONE_DMA` and `ZONE_NORMAL` which have identical functionality to that found in Linux.

### Virtual Memory Management
In the new design, the kernel deals with a one level of paging while the archticture handles the intermediate paging levels. Now each process has defined memory mapped regions wich is either lazily loaded from file, lazily allocated or lazily copied on fork. This allows for very efficient memory use (even the stack is allocated on demand). And finally, EasyOS has no more memory leaks.
