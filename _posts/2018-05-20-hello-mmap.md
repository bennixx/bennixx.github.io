---
layout: post
title: Hello mmap!
---

The moment I have been avoiding for way too long. Finally we have a very very modest implementation of POSIX `mmap`, currently we only support `MAP_FIXED` and only VESA fbdev implements mmap, but it's a good start.

As a demonstration of it, fbterm now uses mmap instead of I/O functions, which should be a lot faster since it now writes to framebuffer directly instead of going through the kernel and I/O functions.

![Image of mmap](/img/mmap.png)

The VMR covering region `0xB0000000` to `0xB0300000` is mapped to VESA framebuffer (using the normal MM API). Private maps will be a lot easier now, with load on demand and all the good stuff in our memory manager :)
