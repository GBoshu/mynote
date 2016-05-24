#poll vs select  
tag: [unix] [linux] [c]

##history
- select() was introduced in 4.2BSD Unix, released in August 1983.
- poll() was introduced in SVR3 Unix, released 1986. In Linux, the poll() system call was introduced in 2.1.23 (January 1997) while the poll() library call was introduced in libc 5.4.28 (May 1997)
- Differences between poll() and select() and to the end some mentions about the more modern event-driven alternatives such as epoll(), kqueue and more. I recommend a library such as libev or libevent.