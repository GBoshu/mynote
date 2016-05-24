#poll vs select  
tag: [unix] [linux] [c]

##history
- select() was introduced in 4.2BSD Unix, released in August 1983.
- poll() was introduced in SVR3 Unix, released 1986. In Linux, the poll() system call was introduced in 2.1.23 (January 1997) while the poll() library call was introduced in libc 5.4.28 (May 1997)
- Differences between poll() and select() and to the end some mentions about the more modern event-driven alternatives such as epoll(), kqueue and more. I recommend a library such as libev or libevent.

##functionality
select() and poll() provide basically the same functionality. They only differ in the details:
- select() overwrites the fd_set variables whose pointers are passed in as arguments 2-4, telling it what to wait for. This makes a typical loop having to either have a backup copy of the variables, or even worse, do the loop to populate the bitmasks every time select() is to be called. poll() doesn't destroy the input data, so the same input array can be used over and over.
- poll() handles many file handles, like more than 1024 by default and without any particular work-arounds. Since select() uses bitmasks for file descriptor info with fixed size bitmasks it is much less convenient. On some operating systems like Solaris, you can compile for support with > 1024 file descriptors by changing the FD_SETSIZE define.
- poll offers somewhat more flavours of events to wait for, and to receive, although for most common networked cases they don't add a lot of value
- Different timeout values. poll takes milliseonds, select takes a struct timeval pointer that offers microsecond resolution. In practise however, there probably isn't any difference that will matter.

##speed
poll and select are basically the same speed-wise: slow.

- They both handle file descriptors in a linear way. The more descriptors you ask them to check, the slower they get. As soon as you go beyond perhaps a hundred file descriptors or so, of course depending on your CPU and hardware, you will start noticing that the mere waiting for file descriptor activity and the following checking which file descriptor that it was, takes a significant time and becomes a bottle neck.
- The select() API with a "max fds" as first argument of course forces a scan over the bitmasks to find the exact file descriptors to check for, which the poll() API avoids. A small win for poll().
- select() only uses (at maximum) three bits of data per file descriptor, while poll() typically uses 64 bits per file descriptor. In each syscall invoke poll() thus needs to copy a lot more over to kernel space. A small win for select().

##portability
**select** - has existed for a great while and exists almost everywhere. A problem with many file descriptors is that you cannot know if you will overflow the the bitmask as you can't check the file descriptor againt FD_SETSIZE in a portable manner.

Many unix versions allow FD_SETSIZE to be re-defined at compile time, but Linux does not

One quirk is that the include header required for the fd_set type varies between systems.

Some - but not all - systems modify the timeout struct so that on return from select, the program can know how long time actually passed. If you repeat select() calls, you need to init the timeout struct each loop!

**poll** - Not existing on older unixes nor in Windows before Vista. Broken implementation in Mac OS X at least up to 10.4 and earlier. Up to 10.3 you could check for a poll() that works with all arguments zeroed. The 10.4 bug is more obscure an I don't know of any way to detect it without checking for OS.

Lots of early implementations did poll as a wrapper around select().

##reference
[orignal](https://daniel.haxx.se/docs/poll-vs-select.html)