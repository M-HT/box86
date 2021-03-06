# box86

![Official logo](Box86Logo.png "Official Logo")
Linux Userspace x86 Emulator with a twist

Box86 will let you run x86 Linux programs (games) on non-x86 Linux, like ARM (host system needs to be 32bit little-endian).

Because Box86 uses the native version for some "system" libraries, like libc, libm, or SDL and OpenGL, it's easy to integrate and use, and performances can be surprinsigly high in some cases.

Most x86 Games need OpenGL, so on ARM platforms, a solution like [gl4es](https://github.com/ptitSeb/gl4es) is probably needed.

Box86 now integrate a DynaRec for ARM platform, providing a speed boost between 5 to 10 times compared to only the interpretor.

Many games already work, like for example: WorldOfGoo, Airline Tycoon Deluxe or FTL. Many of the GameMaker linux games also run fine (there a long list, among them are UNDERTALE, A Risk of Rain, and Cook Server Delicious)

If you are serious about developing Box86, you should install ccache and activate it's support in the cmake project (use ccmake for example)
To have TRACE enabled (i.e. dumping to stdout all individual x86 instructions executed, with dump of registers), you'll also need [Zydis library](https://github.com/zyantific/zydis) accessible on your system.

Some x86 internal opcodes use parts of "Realmode X86 Emulator Library", see [x86primop.c](src/x86primop.c) for copyright details

Here are 6 videos, the first 2 of "Airline Tycoon Deluxe" and "Heretic 2" running on a gigahertz OpenPandora (the second one use the dynarec), and the next 2 of "Bit.Trip.Runner" and "Neverwinter Night" running on an ODroid XU4 (without dynarec), and the last 2 on a Pi4: Shovel Knight (video from @ITotalJustice) and Freedom Planet (video from @djazz), also without dynarec.

[![Play on Youtube](https://img.youtube.com/vi/bLt0hMoFDLk/3.jpg)](https://www.youtube.com/watch?v=bLt0hMoFDLk) [![Play on Youtube](https://img.youtube.com/vi/MM7kWYts7IA/3.jpg)](https://www.youtube.com/watch?v=MM7kWYts7IA) [![Play on Youtube](https://img.youtube.com/vi/8hr71S029Hg/1.jpg)](https://www.youtube.com/watch?v=8hr71S029Hg) [![Play on Youtube](https://img.youtube.com/vi/B4YN37z3-ws/1.jpg)](https://www.youtube.com/watch?v=B4YN37z3-ws) [![Play on Youtube](https://img.youtube.com/vi/xk8Q30mxqPg/1.jpg)](https://www.youtube.com/watch?v=xk8Q30mxqPg) [![Play on Youtube](https://img.youtube.com/vi/_QMRMVvYrqU/1.jpg)](https://www.youtube.com/watch?v=_QMRMVvYrqU)

You can find many more box86 video on [PI Lab Channel](https://www.youtube.com/channel/UCgfQjdc5RceRlTGfuthBs7g) [![PI Lab Channel](https://yt3.ggpht.com/a/AATXAJyMeWrgCjs78gr6To6yX4KtDPUCS7hsbX1rRA=s100-c-k-c0xffffffff-no-rj-mo)](https://www.youtube.com/channel/UCgfQjdc5RceRlTGfuthBs7g).

Compatibility list is there: https://github.com/ptitSeb/box86-compatibility-list/issues

<img src="Box86Icon.png" width="96" height="96">

Logo and Icon made by @grayduck, thanks!

----

Compiling
----
How to compile can be found [here](COMPILE.md)

----

Usage
----

There are a few environment variables to control Box86 behaviour.

See [here](USAGE.md) for all variables and what they do.

Note that now the Dynarec of box86 use a mecanism with Memory Protection and a SegFault signal handler to handle JIT code. That means if you want to use gdb to debug running a program that use JIT'd code (like mono/Unity3D), you will have many "normal" segfault triggering. I suggest you use something like `handle SIGSEGV nostop` in gdb to not stop at each segfault, and maybe put a breakpoint inside `my_memprotectionhandler` in `signals.c` if you want to trap SegFaults.

----

Version history
----

The change log is [here](CHANGELOG.md)

----

A note about 64bits platform
----

Because box86 works by directly translating function calls from x86 to host system, the host system (the one box86 is running on) needs to have 32bits library. Box86 doesn't include any 32bits <-> 64bits translation. So basically, to run box86 on, for example, an ARM64 platform, you will need to build box86 for arm 32bits, and also need to have a chroot with 32bits library.

Also note that, even if, on day, there is a box86_64, this one will only be able to run x86_64 binary on 64bits platform. You will still need box86 (and see 32bits chroot) to run x86 binary (in fact, like it is the case on actual x86_64 linux)

----

A note about Unity game emulation
----

Running Unity games is a hit or miss for now. Unity use mono (which uses signals that are not well emulated enough), and a runtime embedded in the main binary. A solution would be to use a native version of the libmono used by Unity (it can be found here: https://github.com/Unity-Technologies/mono and it needs to be built from source). But the wrapping of this lib is tricky, and not done for now. So the only solution is to emulate everything. The tricky part is to emulated the "JIT" code emitted by mono, and witht he new "protected memory" mecanism implemented, it is running with correct performances now.
You should also note that some Unity3D games require OpenGL 3+, that can be tricky to provide on ARM SBC for now.

TL;DR: mono games are not all working for and can require a high OpenGL profile, but the speed, for the ones running, should be correct now.

----

A note about Wine
----

Wine is not supported yet. Some instruction and syscall are needed (mainly to setup `FS:` segment) that are not correctly emulated yet. It's planned, but not ready yet.

----

Final words
----

(If you use Box86 in your project, please don't forget to mention Box86)
