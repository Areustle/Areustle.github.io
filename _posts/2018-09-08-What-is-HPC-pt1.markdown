---
layout: post
title: "What is High Performance Computing? (Part 1)"
date: "2018-09-08 21:23:23 -0400"
---

![Summit Supercomputer](https://www.ornl.gov/sites/default/files/2018-P01559.jpg)

High Performance Computing is massively parallel computing on many nodes. Each
node acts in concert to solve as large a problem as possible as quickly as
possible. These systems need communication networks that make Ethernet look
like dial-up.There are plenty of advances that speed up regular computing that
we don't consider High Performance Computing (HPC): On chip super-scalar
processing, client, server computing, Vector processing and IP-based networking
being top examples. While HPC employs some of them, so do mobile phones. HPC is
the hardware, applications, compilers, operating systems, networks and tooling
that make this possible.

As of June 2018, the Fastest High Performance Computer is an Oak Ridge National
Laboratory supercomputer named Summit. It has a recorded maximum 200 PetaFLOPS
of compute. That's $2\times10^{17}$, or $200,000,000,000,000,000$ floating
point operations per second.

## Why would anyone do that?

Some problems are just intractable on even the most powerful single machine.
Modern CPUs aren't getting faster anymore, reliably landing between 3-4 GHz,
with some stretching to near 5 GHz. The benefits of parallelism come from more
processing elements yes, but also more memory or cache, and more disks or SSDs.
It may be impossible to make a fast machine 1000 times faster. But it is
possible to take 1000 fast machines and have them work together. It is cheaper
too, and more reliable to use many smaller machines than focus research and
development resources on one large machine. Finally, some problems are so large
they cannot fit on one machine.


## Styles of Parallel Computing

There exist many styles of Parallel computing. The first is also employed by
single chip processors today: Single Instruction with Multiple Data (SIMD). In
this model one instruction is applied to many processing elements at once.
There is one master program counter for SIMD programs. Those elements may be
cells in an AVX register, cores in a multiprocessor, or nodes in a cluster. I
once thought SIMD was limited to all-or-nothing type instructions; add $1$ to
all $n$ elements in a vector for example. I was surprised to learn that
conditional jump instructions are permitted too. This is enabled through an
execution mask: a bitmask where the $i^{th}$ element is set to $1$ if the
$i^{th}$ processor is to execute the instruction in the program counter and $0$
if it is to stay idle.

The immediate counterexample to SIMD is MIMD, or Multiple Instructions for
Multiple Data. In this model each processing element has separate code and does
its own thing, independent of the other processors, though they can and do
communicate via shared memory or message passing schemes.

MIMD requires that a different piece of code be written for each processor, as
such it doesn't scale. A more manageable alternative for human programmers is
to write a Single Program for Multiple Data (SPMD). In such a model each
processing element runs the same user defined program independently, with
separate program counters so as to react independently to different conditions
in the data. As with MIMD, SPMD nodes communicate with either message passing
mechanisms or shared memory.

Finally Dataflow machines operate on instructions in a _just-in-time_ fashion.
Instructions or code blocks await operands provided by other nodes in the
system.  This method automatically finds parallelism as processing elements are
only issued instructions as they become possible to perform, so there's full
employment of each processing element. Instructions produce data which act as
operands for future instructions.

## Communication Networks

High speed networking is crucial to a high performance system. The network must
connect individual processing elements to one another yes, but also connect the
processing elements to memory and I/O devices that may be highly parallelized
themselves.

The key performance considerations for networks are Latency and Throughput.
Latency is the time to first byte. Latency is governed in large systems by
communication overhead and the laws of physics. Light only travels about 30
centimeters in 1 nanosecond, so there is a hard physical upper limit on how
fast a low overhead system can run. Modern systems are butting up against this
limit. Throughput measures the average count of bytes processed per second.

## Network Topologies

High speed communication networks are designed according to a number of network
topologies.  The two simplest are the bus and the ring. A bus is a shared
resource. Each processor can signal each other processor over the bus, but only
1 message can run on the bus at once. A ring connects a node to two of its
neighbors so immediate message passing is fast, while long distance messages
must incur many hops.

Tree topologies form a tree-like hierarchy among nodes. Leaf nodes have only 1
connection while inner nodes have up to $d$ connections depending on the
dimension of the tree. Connections (edges) near the leaves require only small
bandwidth rates, while the root connections require enormous bandwidth. Worst
case transit time for a message is leaf-to-leaf passing through the root. If
the tree is $\ell$ layers deep this incurs $2\ell$ hops. If there are $n$ nodes
in a $d$ dimensional tree this is $2\log_d(n)$.

Mesh networks are like rings extended in $d$ dimensions. In fact a ring is a 1D
mesh. Each processor in a mesh has $2\times d$ connections to other processors.
This gets hard to comprehend once the dimensions exceed 3.

Another high dimensional topology is a hypercube network. Each processing
element is placed at the corner of a $d$ dimensional hypercube and connected to
$d$ other nodes. Thus there are $2^d$ nodes in a system. To expand to $d+1$
dimensions you take two $d$ dimensional hypercubes and connect each node to a
node in the other hypercube. This enables comprehensible connections in higher
dimensional space, but the system scales at $log_2(n)$, which is sublinear, but
too big a scaling factor for systems with many thousands of nodes.

The modern standard interconnection topology is the dragonfly network, which
forms local mesh groups, with group to group connections. This is a great
option that mixes the strengths of meshes with the scaling factor of trees, but
fewer bandwidth issues.

## Memory

In High performance computing we have an open question about how best to manage
memory. First we can decide where to locate it. Do we divide it among the
compute nodes, or use a separate centrally located store. If the later, which
network topology is used to organize it and connect to it? In either case, how
are the processors to access the memory? Do you guarantee full global access
via shared memory? Will access times be universal and equivalent for each of
the nodes? The Former is called UMA or Uniform Memory Access, and the latter
NUMA for Non-Uniform Memory Access. These answers have no simple, single answer
applicable in all cases. As we will often see in High Performance Computing,
the answer is: It depends.

### To be continued

That's all for now. We focused heavily on hardware and organization in part 1.
In part 2 we'll discuss the conceptual and practical programming issues that
High Performance Computer programs face.
