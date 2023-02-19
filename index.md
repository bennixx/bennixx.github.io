---
layout: page
title: EasyOS
subtitle: Hobbyist Operating System, just for the heck of it
use-site-title: true
---

### What is it?
EasyOS is a complete Operating System (kernel + system) for electrical & electronics engineers

### What is cool about it anyway?
- All interfaces implemented so far are POSIX compliant.
- It compiles with -O3.
- Lua 5 is ported to it without changing a single line of code.
- It should be simple for anyone to read and understand the code.

### How does it look?
This is a screenshot of EasyOS running on qemu with 1920x1080 resolution. This is `fbterm` (A framebuffer based terminal) running `aqsh` (Easyos Shell - Part of eobox).

![Image of EasyOs](/img/screenshot.png)

### How to try it?
Simple, just grab a toolchain, build it, patch newlib and ...

Err...

Maybe you're more interested in just an iso image? Head to [Github releases](https://github.com/bennixx/easyos/releases) and grab the latest release iso image there and run it in Qemu with the following command:
- Ubuntu/Debian
`qemu-system-i386 -enable-kvm -cdrom easyos.iso -m 1G`
- Fedora
`qemu-kvm -cdrom easyos.iso -m 1G`

If you're a hardcore programmer (and know-what-you-are-doing) though, you may reference [this post on OSDev.org](https://forum.osdev.org/viewtopic.php?f=2&t=32714) and follow the steps. Or wait for a complete step-by-step guide if you don't know what the heck that is.

### Is that it?
Yes, that is it, that's all there is, maybe you're intereseted in fancy GUI? specific device driver? support for new filesystem? Feel free to contribute to the repo, or wait until I do it.
