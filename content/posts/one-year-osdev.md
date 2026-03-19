---
title: "One year of developing my own operating system"
date: 2026-03-20T00:00:59+01:00
draft: false
---

Writing an operating system from scratch is something I always wanted to do, and [last year](https://github.com/MONOLITH-Project/MONOLITH/commit/81f8023feb17f0d61868010e3d37ab7b94b79aa6), I started working on [MONOLITH](https://monolith-project.org), an operating system meant to make my dream true.

This operating system is meant as a fun learning experience to know more about operating systems, and to improve my programming skills in general.

## 1st month: GDT, IDT, Memory Management and the console

- **29/03/2025**: The first step when developing an operating system is _usually_ getting it to boot, here I used [multiboot2](https://www.gnu.org/software/grub/manual/multiboot2/multiboot.html) protocol boot protocol.

- **30/03/2025**: I initialized both the [GDT](https://osdev.wiki/wiki/GDT_Tutorial) and the [IDT](https://osdev.wiki/wiki/Interrupt_Descriptor_Table).

- **31/03/2025**: Implemented some functions for logging to serial.

- **03/04/2025**: Implemented a [physical memory manager](https://osdev.wiki/wiki/Brendan%27s_Memory_Management_Guide#Physical_Memory_Management).

- **05/04/2025**: Implemented a heap allocator.

- **11/04/2025**: Added support for i386 architecture.

- **15/04/2025**: Implemented a VGA console.

- **28/04/2025**: Implemented a VESA video driver for i386.

![Screenshot](month-1-screenshot.webp)

## 2nd month

No interesting developments happened during that month, mostly due to college distractions and lack of motivation.

## 3rd month

- **02/06/2025**: Switch from multiboot2 to [Limine boot protocol](https://codeberg.org/Limine/Limine).

- **03/06/2025**: Drop i386 and Text VGA mode support.

- **08/06/2025**: Add support for [virtual memory management](https://osdev.wiki/wiki/Brendan%27s_Memory_Management_Guide#Virtual_Memory_Management).

- **10/06/2025**: Add support for [PIT timer](https://osdev.wiki/wiki/Programmable_Interval_Timer).

- **18/06/2025**: Implement a [VFS](https://osdev.wiki/wiki/VFS) and a tmpfs.

## 4th month

- **23/07/2025**: Add support for an [initrd](https://osdev.wiki/wiki/Initrd).

- **30/07/2025**: Implement an ELF loader.

![Screenshot](month-4-screenshot.webp)

## 5th month

- **12/08/2025**: Initial ring-3 and syscalls support.

- **18/08/2025**: Ported [microui](https://github.com/rxi/microui)'s demo.

- **19/08/2025**: Added support for keyboard input in userspace.

![Screenshot](month-5-screenshot.webp)

## 6th month

- **13/09/2025**: Added some disk I/O syscalls.

## 7th month

- **20/10/2025**: Initial (and half-baked) support for task switching.

## 8th month

- **26/11/2025**: Moved from GitHub to [Codeberg](https://codeberg.org/MONOLITH-Project/MONOLITH/).

- **29/11/2025**: Implemented a userspace libc.

- **30/11/2025**: Crash handling for userspace processes.

## 9th month

- **05/12/2025**: Prototyped a desktop environment using Claude Opus 4.5.

- **05/12/2025**: Implemented address space separation between processes.

- **06/12/2025**: Removed the kernel text console.

- **08/12/2025**: Implemented userspace malloc, realloc and free.

- **22/12/2025**: Refactored mouse/keyboard input syscalls to use event polling instead of callbacks.

- **28/12/2025**: Implemented scheduling.

![Screenshot](month-9-screenshot.webp)

## 10th month

- **05/01/2026**: Implemented libgfx.

- **15/01/2026**: Rewritten the desktop.

- **19/01/2026**: Initial IPC implementation.

- **30/01/2026**: Add support for drawing the wallpaper.

- **31/01/2026**: Implement proper framerate locking.

![Screenshot](month-10-screenshot.webp)

## 11th month

- **15/02/2026**: IPC refactoring and optimization.

- **21/02/2026**: Added support for shared memory.

## 12th month

- **03/03/2026**: Initial work on the libdesktop window protocol.

- **04/03/2026**: Desktop performance optimization.

- **06/03/2026**: Added support for mouse/keyboard events to libdesktop.

- **06/03/2026**: Ported Doom.

- **12/03/2026**: More performance optimization for the desktop.

![Screenshot](month-12-screenshot.webp)

## What's next?

This was the result of twelve months of work on this project. Despite being busy with many other things, I achieved a decent amount of progress and had a lot of fun doing so!

There's a still a lot of work to be done on this operating system in the next year, including (but not limited to):

- A UI framework.
- A software packaging format.
- Disk drivers.
- File systems.
- Permissions/Capabilities.
- More improvements on the desktop.
- More desktop apps.
- SMP.
- PCI drivers.
