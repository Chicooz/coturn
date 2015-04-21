# TURN relay server performance: load balance and network optimization #

This TURN server uses a mixed operation model similar to one used by node.js and nginx: an asynchronous IO is combined with the threading model to utilize multi-CPUs hardware. The number of threads is limited (close to number of CPUs) and the resource consumption is optimal. Many client connections can be handled concurrently. The TURN Server tells the operating system, through epoll (Linux), kqueue (BSD and Mac OS X), event ports (Solaris), poll (Cygwin) or select, that it should be notified when a new event arises, and then it goes to sleep. When a new packet arrives, or when a new timer event happens, then the TURN Server wakes up and it executes the callback. Each per-event operation is very short so the system has a near-real-time reaction time (as it is necessary for efficient media traffic handling).

Of course, in the high load scenario when many TURN sessions are involved, the turnserver process has virtually no sleep time, and it operates in a semi-pull mode.

For the relatively long-term actions (database interaction, CLI handling) the TURN Server uses separate threads, so the traffic-handling processing is not affected by the longer operations.

The system is implemented in C language, for efficiency and for portability. For IO and timer events multiplexing, the libevent2 library is used.

The TURN Server also incorporates some optimizations specific to a network protocol server, for example some elements of "pull" processing are included (as opposed to the usual "push" approach).

Each TURN session (allocation) consumes relatively few resources:

  * an allocation does not have its own thread or process;
  * an allocation usually stays within the same thread, reducing context switching and locking;
  * an allocation shares lots of system resources with other allocations.

Of course TURN sessions still require resources and they still consume CPU power and network throughput. Each system, no matter how large it is, still can handle just a limited number of TURN allocations (thousands or tens of thousands of usual traffic streams). This document is about maximizing that number.

## The things affecting the max number of concurrent sessions: ##

  * Media group:
    1. Codec to be used (payload size).
    1. Packet interval.

  * Network topology and protocols group:
    1. ISP performance.
    1. In each peer-to-peer session, how many TURN sessions are involved - two (when both peers connect to the TURN server) or one (when one peer is talking to the relay endpoint).
    1. Which protocols are involved (TCP, UDP or TLS).
    1. If TLS is involved, then the parameters of the TLS sessions (how heavy they are) are important.
    1. Can your client use alternate-server 300 error for balancing ? (WebRTC client cannot, as of now).
    1. Can you use round-robin DNS for balancing ?

  * Hardware, OS and software settings group:
    1. Number of NICs.
    1. How advanced are the NICs.
    1. System memory and bus performance (lots of traffic is going thru).
    1. Number of CPUs and CPU performance (cache etc).
    1. OS type and version.
    1. How well NICs are integrated with your OS (whether your drivers are using full NIC capabilities).
    1. How many file descriptors the system can handle (overall and per-process limits). Usually this is ulimit system parameter, see your system manual how to increase per-process and system-wide ulimits. Each TURN allocations takes at least 1 file descriptor or more (different types of sessions consume different number of file descriptors, and different network engines behave differently in that regard). If you are trying to open more sessions than your system limits allow, then you will see an error like "Too many open files" in the TURN server console or in the log file.

## Database optimization ##

The fastest database option is SQLite - because there is no inter-process or network communications involved, the database management library is linked directly into the TURN server process. But you may need a more robust database management system (like PostgreSQL, MySQL, Redis, or MongoDB) if:

  1. your database is more than a few thousand records;
  1. if extra security is required;
  1. if other processes are going to access, actively, the TURN database simultaneously with the TURN server;
  1. if the database is shared among several TURN servers.

In a lean and simple setup, SQLite is the simplest and the fastest option.

## Network operations performance optimization ##

The TURN Server performance very significantly depends on how efficiently the operational system handles the TCP/IP stack operations. Performance optimization may be achieved by combination of several approaches:

  * ### High-end NICs with RSS feature (Receive-side scaling). ###

If you have nice expensive NICs with optimal drivers, then you can turn ON the RSS feature, and that will help to distribute the network load among multiple CPUs. The TURN server is inherently multi-threaded application that was designed to utilize multiple CPUs. But the network packet receiving kernel process may become a bottleneck. RSS is a way to overcome that bottleneck.

See also the next section.

  * ### "Software RSS" : Receive Packet Steering (RPS). ###

If your network card does not support RSS, then your can use an OS that emulates that functionality in software, for example recent versions of BSD and Linux. Read the documentation on your OS to find out how to turn ON RSS and RPS. General description what RSS and RPS are can be found here:

[Scaling in the Linux Networking Stack](https://github.com/torvalds/linux/blob/master/Documentation/networking/scaling.txt)

  * ### Load balancing among multiple systems. ###

This is the best and most efficient performance tuning approach. It allows near-linear performance improvement - proportional to the number of available systems; virtually unlimited scalability can be achieved.

You have three options here:

  1. Set a networking load-balancing equipment that redirects the requests to a member of the TURN servers group. In general case, it must take care about redirecting the requests to the same server from the same client IP - because some TURN sessions from the same client must share the information. There are two cases when different TURN sessions must interact: RTP/RTCP connection pairs (from RFC 5766) and TCP relay (from RFC 6062). If you are not using those features then a simple network load balancing is enough. If you do use those features, then you have to map the whole client IP (with all its network ports) o the same TURN server. Also, if you are using the mobile TURN (from the new MICE draft) then you cannot use the network load balancer option because client sessions from different IP addresses must interact - so you have to use the next option (see below).
  1. Set a less complex scheme with round-robin DNS. The client must send all its requests to the same DNS-discovered TURN server. That scheme supports all use cases.
  1. Use build-in balancing capability with ALTERNATE-SERVER option (--alternate-server options). In this case, the client must also send all requests to the same alternate-server address. You set a single system as the "front-end" of the cluster of TURN servers, and that "load banacer" system does nothing - it just returns 300 ALTERNATE-SERVER error to all clients, with an alternate server IP address, so the client will re-connect to another server. If the alternate servers are chosen in a round-robin manner, then you have	a load-balancing cluster of TURN servers.

  * ### Load balancing and optimization within a single system. ###

This section is mostly for Linux systems with pre-2.6.32-431 kernels, or for kernels from 3.0 to 3.8. Examples are CentOS 6.4 and Debian Wheezy. The Cygwin users can apply the same technique, too.

This section is **not** applicable to:

  1. systems with Linux kernel 3.9 or newer, like ArchLinux, Debian Sid, Fedora;
  1. systems with latest Linux kernel of 2.6 line (2.6.32-431 or newer, like CentOS 6.5);
  1. BSD systems;
  1. Solaris systems;
  1. Mac OS X;

- users of those systems can skip this section. Your TURN server architecture is very finely tuned for your platform and it is already reasonably optimal as-is; when the turnserver process starts, it chooses automatically the best performance option for your system.

If you are using an older Linux system system, great performance is still achievable - read on.

As it was said before, the TURN Server performance mostly depends on how efficiently the operational system handles the TCP/IP stack operations. Usually, the TCP part of the stack is properly optimized, but the UDP handling is often suboptimal - so far, a typical UDP stack implementation is not very well tuned for the "persistent" UDP sessions like ones used in TURN. For example, by default the Linux kernel hashes all UDP sockets in just 128 buckets; if you have thousands UDP sessions then you have lots of UDP sockets which are handled inefficiently.

In other words, the implementation of UDP in the Linux kernel makes use a hash table to store socket structures. In older 2.6.32 kernels (default in CentOS 6.0-6.4, for example) this hash table is hardcoded to have 128 entries. So with a large number of sockets, the performance of the table degrades to a linked list which must be traversed for each incoming packet.

If you have a Linux kernel before 2.6.33 then you can change the hardcoded hash size in the kernel code and re-compile the kernel. If you have a more recent kernel then you can do that without kernel recompilation. Kernel 2.6.33 introduced a configurable UDP hash table size, and a second UDP hash table, keyed by IP+port (previous to this it was only by port). You can configure the hash table size by setting the "uhash\_entries" boot-time kernel variable (for example, in /etc/grub.conf). For best performance, set it to 65536.

Other OSs have similar issues. Check documentation to your OS for
configuration instructions.

The TURN Server is designed as a multi-threaded network packets routing system. But multi-threading is not always the best option for a particular
system configuration. The default start-up TURN Server configuration is a compromise between memory, thread affinity, cache and CPU resources, and between TCP and UDP typical "vanilla" OS networking stack implementations. This is not a specialized system tuned for a very particular hardware, a particular OS or a particular load pattern. The TURN Server design is tuned for very wide range of possible applications, so it may not be 100% most optimal for a particular application on the particular platform. But it must be good enough to be usable everywhere.

The TURN server parameter -m allows tuning of the threading configuration. You can turn off the multi-threading by using "-m 0" parameter (or "--relay-threads=0"). It will keep all network packets processing within one thread, eliminating context switch etc - it may be a more efficient option for your system. There still will be separate authentication threads because the authentication process must not be holding the normal packets routing.

You can use "-m 0" option and run multiple TURN servers on your system, one per CPU core. That would be probably the best performance option in terms of scalability. Each TURN server must have its own network listening address and its own relay IP and/or relay ports range - so the configuration will be complicated - but the performance will be the best possible. You can use ALTERNATE-SERVER mechanism to present the whole "pack" as a single "initial" TURN server front-end to the external world.

  * ### Efficient events multiplexing ###

For a large-scale TURN server, efficient handling of the multiple sockets may become the most serious problem. The TURN server uses libevent2 tool for that purpose. Libevent2 utilizes the most efficient events multiplexing facility available on the current platform:
  * Linux: epoll
  * BSD and Mac OS X: kqueue
  * Solaris: event ports
  * Cygwin: poll

Among those facilities, kqueue is probably the most advanced:

[Scalable Event Multiplexing: epoll vs. kqueue](http://www.eecs.berkeley.edu/~sangjin/2012/12/21/epoll-vs-kqueue.html)

So with a huge number of sockets, a BSD system may be an attractive option, especially when TCP protocol performance is critical. For example, the DragonFlyBSD is a performance-oriented BSD variant. FreeBSD is a good choice, too.

When the client traffic is mostly over UDP (or DTLS), then the most recent Linux kernel versions (with Google networking patch) may have an advantage. The recent Linux kernels allow UDP processing in multi-threaded environment over just a few "front-end" sockets (all sessions belonging to the same thread are sharing the same UDP socket). The less number of sockets, the more efficient is the traffic processing by the event multiplexing library. So the systems with the Google kernel patch have an edge (CentOS 6.5, ArchLinux and Fedora, at the time of the writing).

Our combined tests (which include all types of various loads) usually show that FreeBSD 9.x and DragonflyBSD are the best performers as a platform for the TURN Server. It is very difficult to analyze why. But, or course, your mileage may vary.