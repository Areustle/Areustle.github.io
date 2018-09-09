---
layout: post
title: "Lecture 2"
date: "2018-08-30 13:59:46 -0400"
---

Introduction Continued
======================

## Coordination

As multiple processors are working together to solve 1 problem, the different
processes must synchronize. There are 2 main forms of synchronization:
Protection of a single shared object (locks) and the coordination of different
processors (barriers). Locks allow one node to use the resource at a time.
Barriers require that all nodes reach a certain instruction (the barrier)
before proceeding.

The size of a unit of work by a processor must also be solved. This is called
"grain" size, as in coarse grain vs fine grain. There are 2 main issues to
manage load balancing and coordination overhead. Load balancing is the goal to
give processors equal amounts of work. Coordination overhead is the cost
of coordination that doesn't go directly toward solving the problem. Ideally
you want all process to complete at the same time, while not spending too much
effort ensuring that.

## Sources of Parallelism

The 2 main forms of parallelism are Control Parallelism, and Data Parallelism.

Statements are control parallel; the flow of control is handled in parallel.
Thus series of steps can be done concurrently. This is the basis of dataflow
computers.

Loops are data parallel. Data parallelism is the most common source of
parallelism in most programs. Each processing element gets 1 iteration to
perform. This scales well because you don't need to write more programs, you
can run the same programs on more and more data.

## Examples of Parallelism

Embarrassingly Parallel or Pleasingly Parallel problems are easy problems
solvable with multiple independent jobs. The jobs need not interact or need
only interact as a final step to gather completed data. These are effectively
different simulations running with little or no communication.

Scientific parallelism sees dense linear algebra operations or physical systems
simulated. Either they divide up the matrix into chunks or tiles, or they
divide up the physical space into a discretized grid. This employs some
communication between the elements of the sub-problem, usually only at the
border region. By building bigger, faster HPC systems this grid can be made
ever smaller and thus more realistic.

Database parallelism is the biggest success of parallel computing, with
companies like Oracle delivering highly parallel solutions to the issue of
resolving SQL queries. The queries are all handled in parallel thanks to the
unordered nature of relational algebra.

Artificial Intelligence tasks often simplify to searching a space or pattern
recognition problems. Much of that can be solved via a divide-and-conquer
approach and accomplished in parallel.

## Metrics in Application Performance

Speedup is the ratio of execution time on one node to execution time on $n$
nodes. You hold the problem size fixed and compare to an optimal serial time
algorithm. The goal is to achieve linear speedup. When transitioning to a
parallel algorithm you will likely incur some added coordination overhead.
Ideally coordination overhead won't kill your speedup.  Such a case is called a
"Strong Speedup". Super-linear speedup is possible due to adding more
memory/cache/network bandwidth/disk IO. In such a case, some other resource was
the bottleneck, now it isn't anymore; Or it was a reordering of a luck-based
problem, like a space search. With more searchers there's the chance that one
will get lucky faster and find the solution sooner.

Overhead metrics can be thought of as the computation to communication ratio.
You'd like to maximize this ratio so most of the time is spent in computation
and minimize communication.

ISO-Speedup (scaled speedup) occurs when we scale the problem size up with the
number of nodes. Doubling the resources should permit you to double the problem
size. The Goal is a flat horizontal curve. Through this we can solve bigger
problems, or the same problem at a finer resolution. This is also called "Weak
Speedup".

## Amdahl's Law

Amdahl's Law is an equation that limits the maximum speedup of a program
as a function of the proportion of the code that MUST BE SEQUENTIAL. The
rest of the resources can approach infinity, but the time spent in the
serial section of the code quickly dominates the time of the program.

In the equation of Amdahl's Law $p$ is the fraction of the code that is
parallelizable, while $n$ is the number of parallel nodes added.

$$
\text{Speedup}=\frac{1}{(1-p)+\frac{p}{n}}
$$

![Amdahl's Law](https://upload.wikimedia.org/wikipedia/commons/e/ea/AmdahlsLaw.svg)

Even if 90% of our code is parallelizable, we can only hope to achieve a
speedup of 10x.

## How to Write Parallel Program

A few strategies exist when looking to create parallel programs.

First you could use old serial code and have the compiler convert to a parallel
program for you! This never works in practice. It's called the dusty deck
problem after the propensity for using code so old it only exists in a deck of
punch cards.

Most popularly, you write a new program using an existing serial language plus
a parallel communication library like MPI. This requires no compiler changes,
but does require thought and effort on the part of the programmer.

Alternatively you could use a New Language(!) for parallel computing. This
requires you to rewrite all your code and it's hard to create a language that
provides high performance portably across many different platforms. This would
be great were it not for the extreme diversity in HPC environments.

Finally you could use a Hybrid approach like OpenMP. This will parallelize
some serial code but not all. OpenMP and MPI together are a common solution
preferred by many scientists because it is easy!

## Application Example - Weather

Let me finish with an example of the kind of problem generally solved using
HPC systems: Weather modeling. We do a lot of this on the global scale at NASA
to better understand climate change.

This is typical of many scientific codes, you compute results for 3 dimensional
space at multiple time steps using equations describing the physics and
chemistry of the problem. The idea is to use a discrete grid simulating the
continuous problem space. Ideally as the grid gets finer grained the speed and
accuracy will increase. Programmers also include simplifications of the
problem, for example assuming a spherical earth and no mountains etc.

Grid points divide our continuous space into discrete parts along Latitude,
Longitude, and Altitude. This also demands some design choices like, which face
or corner of the voxel does the data represent? The center? One edge over
another?

The programmer encodes how each grid cell will interact with each other grid
cell as time progresses. The calculation of the behavior of a group of grid
cells is the responsibility of a processing node in the cluster. That node must
communicate with neighboring nodes to understand how border cells are affected
by the states of cells calculated on other nodes. Once the communication is
finished, the whole cluster moves to the next time step and repeats. This occurs
until the programmer decides to stop the program, either at a prearranged time,
or when sufficient error has been accumulated by the simulation. The final state
of the system and the path it took to get there informs the weather forecast.

## Conclusion

That about wraps up our top level overview of High Performance Computing. Not
every problem can be best solved with HPC, but those that can are big and
getting bigger!

Next time we'll discuss the practical issues of writing communicative code by
looking at MPI and one of its inspirations: PVM.
