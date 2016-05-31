---
layout: post
title:  "A smarter SmartOS?"
date:   2016-05-31 10:12:58 -0500
categories: smartos design
---

I've always been fascinated by operating system design, although it's obviously 
much more difficult to actually engineer one. I believe that all great systems come about as the result of thoughtful design. So these are some thoughts!

# On compilers & linkers...

## Where GNU has won...

The GCC and GNU ld(1) frontends are essentially the gold standard for Unix 
linker and compiler interfaces. There are at least three linkers 
(gold, lld, GNU ld) and three compilers (gcc, clang, libfirm/cparser) 
implementing these command line interfaces, so you are minimally locked in and 
actually somewhat empowered by adopting them.

GNU make(1) 3.81, the last GPLv2 release, ships with MacOS X now and likely 
forever. It will build on essentially any platform with only the help of a 
compiler, which is a great feature for a build system. I actually do like 
BSD make, but I don't think it would be baseless to say that GNU make has 
much wider adoption in the open source community. 

## Where LLVM has won...

The modular design ethos of LLVM has resulted in great tooling and many 
languages have now been implemented in terms of LLVM. The clang static analyzer 
is much more extensible and introspective than any version of lint, and it is 
also much easier to use in a project. Being sponsored by Apple and used by both 
MacOS X and FreeBSD is a huge feather in the cap, 

# On operating systems...

## Where MacOS X has won...

Like it or not, OSX is Unix on the desktop and it's pretty damn good. The 
development environment is very solid, sporting clang, libc++, libdispatch, 
Debug Fission, lldb, dtrace, and more out of the box.

It is reasonable to expect that a great many developers interested in using 
SmartOS will be working on Macs (or Hackintoshes). I think this is a vector 
that could attract commercial developers and commercial workloads to SmartOS, 
and Triton by extension.

## Where Linux has won...

Moreso than the license or the sometimes entertainingly polemic leadership, I 
think the defining attribute of the Linux kernel is how easy it is to iterate 
on it. Virtually anyone can alter some code then do a `make menuconfig 
&& make && make bzImage` and end up with a working Linux kernel, and this has 
resulted in a huge community of ~20,000+ contributors.

However, because Linux is just a kernel there is relatively little unification 
in userland. Binaries from one vendor are all but guaranteed not to work on 
another which can become hugely frustrating. Even the libc is not standard, and 
there are several to choose from. This is both a strength and a weakness.

The stable syscall ABI has resulted in Linux being implemented on SmartOS, 
FreeBSD, and even Windows. One can almost think of the Linux syscall ABI as the 
lowest common denominator of Unix! This stability has allowed the emergence of 
Docker, but the full benefits of containerization cannot be realized unless it 
is fully baked into the system as it is with Jails and, to a greater extent, 
Zones. Linux doesn't actually have containers, it has namespaces and cgroups.

Security and good design cannot be approached asymptotically in the same way 
that high performance can. It seems necessary to think about the abstractions 
beforehand - measure twice, cut once. 

## Where Illumos/SmartOS has won...

The attitude toward risk is what defines these communities, and the emphasis on 
backwards compatibility, production workloads, observability, and post-mortem 
debugging has set Solarish platforms apart from the pack for serious 
mission-critical users. Although there are fewer contributors, they tend to be 
experienced commercial developers and subject matter experts.

The downside is that the system moves somewhat slowly relative to Linux. This 
is both a strength and a weakness because it is frustrating when things break 
but, e.g., SVR4 and IPS packaging kinda suck.

# Bringing It All Together...

## Building stable syscall ABI...

A stable syscall ABI shouldn't be where you end but where you *start*.

The Linux ABI and Docker are good enough, that's true. It does work. However, I 
think a carefully designed pure 64-bit ABI and corresponding userland designed 
from the ground up beginning with the syscalls and moving up the stack to a 
musl-based libc intended for Docker containers should be much better. 

## Brands for backwards and forwards compatibility

There is no reason to abandon the Illumos or Linux communities and no sense in 
it! Full compatibility should be attainable via judicious use of brands to move 
the new design forward and perpetuate existing software.

I feel that the ABI should begin life as a branded zone and gradually move 
toward subsuming the global zone as it evolves. Once the ABI reaches maturity,
it would become the hot path in the kernel - no need for data model checks, 
it's always native 64-bit! I think that this would likely enable some more 
aggressive optimizations in the kernel and certainly result in smaller 
Alpine-sized Docker images with no need for multilib support.

## Making it faster to iterate...

I think it is very feasible to combine Linux style usage of kconfig with a 
FreeBSD style unified tree containing a fully functional, bootable system.

This is exactly what I am working toward at github.com/hypoalex/smartos

The distinction between the global zone, child zones, and Docker images should 
be baked into the build system at the highest level. 

### The toolchain really matters!

Adopting GNU make 3.81, clang, and lld would bring SmartOS to parity with MacOS 
X and allow drop-in compatibility with the vast majority of extant programs and 
their attendant build systems. I'd love to see an Xcode plugin so that a Mac
becomes a first class development environment for SmartOS.

# In closing...

I have more thoughts on this, I just wanted to write some of them down!


