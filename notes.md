Hello everyone

this work introduces a novel network partitioning and multi-leader election algorithm

I'd like to start by dissecting the title

---

Let us start from the core, leader election

---

Leader election algorithms select a single node in a network (the leader),
usually to assign coordination or special tasks to it.

It is used for a variety of task, primarily breaking symmetry in networks of peers,
often as building block for other algorithms that require a seed node;
for instance, a sink where to send data collected by all devices,
or find a central coordinator,
or enforce a single access point.

Now, the second keyword here is *multi*

---

Unsurprisingly, multi-leader election happens when
more than one leader is elected.

---

Multi-leader election algoritms are used in several context,
they are the base block upon which network overlays can get built
(even on multiple scales)

Or they can be local data collection hubs
+

Or coordinators for a sub-network

More in general,
local leaders could be used to coordinate activities where the scale
is in-between device-local and global

---

Multi-leader election per-se does not say *how* leaders are selected.
In this case, I picked them randomly,
which works for some applications,

---

in this case, for instance,
all devices in the network are battery powered and a full battery,
so there is no reason
(besides maybe centrality in the network)
to pick one over another.
But what if, instead, the situation with the battery level was this one

---

if you ask me,
I'd like my leader nodes to be as stable as possible,
so I would prefer

---

those five with a full battery over the others.
If other nodes get charged and these discharge instead,
then I would like the leader to change, maybe.

---

Being able to express a dynamic preference over which nodes should be
selected makes the algorithm "priority based".

I exemplified with the battery level,
but of couse it may be that some nodes are static and others are mobile,
or it could be that some are computationally better equipped,
or with a cable versus mobile network,
or better positioned in a peer-to-peer network;
there are plenty of reasons some nodes could be selected over others.

The next keyword

---

Is self stabilisation, and requires some thought.

Self stabilisation is the ability to converge
to correct states

+
in finite times

+
from any arbitrary initial state

+
In short, you can perturb the system the way you want,
if at some point the input remains stable,
then the system converges.

+
It is not a new notion of course, as far as I know it was first introduced
by Dijkstra in 1974

Self stabilisation is a very desirable property,
and I think that it is better illustrated by showing what may happen if
self-stabilisation is not there

---

This is a non self-stabilising multi leader election that we built
along the road that led us to the current contribution.
It seemed to self-stabilise in a lot of cases,
but then we found this counter-example

In this network, regardless of stable inputs, the system oscillates.
Imagine trying to cordinate activities within a partition that gets continuously disrupted.

This is why want our algorithm to be

---

self-stabilising.

It does not imply that is perfect of course,
in particular self-stabilisation tells us nothing about 
the duration and quality of the transitory.

---

The last thing that we want is a way to use the leaders.

Commonly, once leaders are selected,
we want to assign

---

every device to its leader,
partitioning the network.

Often, first leader are elected and then partition get formed,
but we'd like, if possible, to elect and partition in a single shot.

---

Of course there are other algorithms that do a similar job,
one is "sparse spatial choice" from 2014,
designed for multi-leader election,
it was a source of inspiration for our work,
but as far as I know it has never been formally demonstrated to be self-stabilising.

+
+
There are also other two works that are similar and more recent,
one first presented at eCAS and recently extended,
which are self stabilising.
They use feedback loops and rely on diameter estimation,
they are conceived for single leader election but can be tuned
to elect multiple leaders.

However, we wanted to be able to partition the network without
using operations of information collection,
using solely propagation,
which should be faster

---

Let me summarise our goals:
we want an algorithm that can

+
elect multiple leaders
+
with explicit priority controls
+
and partition size
+
we want to be able to use arbitrary
+
distance metrics
+
and no feedback loops,
+
+
information diffusion only.

---

The core idea is actually pretty simple, but if taken trivially it has some problems.

First
+
make yourself a candidate, as you know no one initially

Then
+
select the best leader including information from neighbours

Then
+
find the best leader and tell your neighbours

Of course,
+
if the best leader is too far away,
ignore all information regarding it from any source, de facto
+
creating a network partition

---

Let's quickly see how it would work,
in this network every node is associated to its priority as a leader

To simplify,
we assume that at every slide every device executes one round,
in which every node
computes, using the messages it received at the end of the previous round,
and then sends messages to its neighbors

At the beginning of the computation, nodes start with no information

---

At the first round, knowin nothing about anyone else,
they declare themselves leaders,
and gossip the information to their neighbors

---

Nodes that receive the information about the existence of
a better leader join its partition.

Some nodes enter an inconsistent state
(here depicted with a gray filling),
where they select as leader a node that has already joined another partition

---

At some point the information reaches the maximum partition size

For simplicity, here I set two hops, but any valid metric would work

Reaching the maximum partition size means that some nodes
will get notified that a better leader exist in their network
that they cannot bind to, which in turn

---

makes them discard all information coming from the previous network
and restart the computation

---

As you can see, this disrupts even the pre-existing partitions

---

that look pretty legit

---

and that, in fact, need to grow again

---

At some point all nodes have the information of the best leader
and  are computing the next-best off the area claimed by the first leader

---

Yet, again, the second-best leader area is limited,
thus a new network segmentation plus restart happens

---

Which leads to another iteration

---

---

---

Until we finally converge, in fourteen steps

---

Now, even the trivial idea has some merits,
starting from the fact that
+
it works

It also uses solely diffusion and no accumulation

and has a very succinct implementation in some frameworks,
this is, for instance,

+
how it can be implemented in Protelis,
a framework for aggregate computing

The details are not important, it is just to show that it can stay in few lines of code.

but not all that glitters is gold, as the algorithm
+
is not self-stabilising due to the gossip not being self stabilising
this is somewhat fixable with the appropriate techniques,
but they complicate the algorithm

Also, as we have seen with the running example,
it discards information even when it is useful,
for instance, we had to rebuild that second partition,
led by node 28, that looked pretty ok upfront

---

The trivial idea, however, is a good way to understand
the contribution of this paper,
which we call
Bounded Election

In fact, in essence,
the base idea is to parallelise the computation instead of gossiping recursively,
and then compete at the border instead of giving up and restartings

More in detail,
+
Every opinion on a leader is a triple which includes:
the leader priority
the distance from the leader to the current node
the leader identifier

As before, at the beginning everyone candidates itself

Then the best is selected among itself
and the best leader every neighbor communicated back to us,
excluding candidacies that propose the node itself,
or those whose path to the current node would be too large for the maximum partition size

The best leader that is found this way is then communicated to the neighbors

---

Let us see it running and compare with the trivial version

We start from the same emtpy network 

---

And the first two steps

look like the same 

---

Indeed, the behaviour is identical until the first partition gets formed

---

At this point, things change:
there is no restart,
the partition led by node 28 is deemed valid,
de facto leading to a large chunk of the network already stable.

The computation continues

---

in the remainder of the network 

---

Until the network stabilises, in eight steps instead of fourteen.

---

Bounded election pros:
+
well, of course, it works
+
and uses diffusion only
+
it has reasonably succint implementations
+
stabilises multiple partitions in parallel
+
supports custom priorities and metrics
+
and we have proven it to be self-stabilising

+

There is also a con, though, that is present in the recursive version, too:
there are pathological cases in which the algorithm works pretty badly
due to the enforced prioritisation

The base idea is that a very high priority node
"dances" in and out the border of the highest priority partition;
if nodes are sufficiently inconveniently placed, it may cause network-wide re-adjustments.

---

I'm not going through the proof of self stabilisation in this presentation
as I think it would get most you sleepy
but I can explain the proof structure quickly:
we picked a demonstrably self-stabilising fragment
known as "the minimising share"
and rewrote parts of it with changes that preserved its self-stabilisation
and after several re-writes we arrived to a valid implementation of
Bounded Election, that is in turn self-stabilising

---

Now, as said self-stabilisation is super-nice,
but tells us nothing about the transitions.

There may exist self-stabilising algorithms that are unusable in practice in
many scenarios as their convergence time is too long,
or their transient is too noisy.

We thus evaluate the proposed algorithm in three scenarios:
in a scale free network where we switch the priorities,
in a scenario where there are higher-priority static devices
and lower-priority mobile devices,
and finally a scenario where every node moves randomly inside an arena.

---

We are interested in how stable the partitions are,
and measuring this kind of thing is not easy,
we had to build a custom metric

we concocted a metric of instability,
so the lesser the better,
that looks behind 10 seconds,
counts how many times every device changed opinion about its leader,
and divides the number by the number of times it could have changed.

^

In the paper is better formalised, of course

---

We compare three algorithms:
bounded election,
the trivial version of the idea,
and the existing implementation of sparse-choice,
that we selected among the others in the literature as the only one that relies on propagation without recollection,
and hence that we expected to converge the fastest

+

For each scenario we ran 200 repetitions
the whole experiment can be reproduced
the code is open source, implemented in Protelis and Scafi,
and we used Alchemist to simulate

---

On scale free networks, we expected sparse choice and bounded election to perform similarly,
with the latter having an advantange where leader nodes were more "central" in the network,
and we expected the recursive version to be the worst

+

We observe instead that bounded election outperforms the other algorithms in all the conditions

We also note that although never proven to be self stabilising,
the classic sparse-choice always stabilises in our experiments

Changing the leader priority from the node degree to a distribution that
does not consider the shape of the network has little impact on bounded election
and on the classic version,
but a considerable impact on the recursive version,
that works even better than the classic one when high-degree nodes have higher priority

---

With a mixture of static and mobile nodes,
we expect that the ability to pick the static ones as better leaders
should favor both bounded election and the recursive version over
the classic sparse-choice

+

We observe that bounded election indeed outperforms the other algorithms,
and we can also observe that every some time the recursive implementation
leads to disruptions and re-adjustments of the network,
so even though the the classic sparse-choice tends to produce a little more instability throughout the experiment,
it may still be preferable if little continuous instability is better
than large and less predictable disruptions

---

With all nodes moving randomly, our expectation is that the classic
sparse-choice works best,
as we do not enforce any meaningful priority,
and movements should induce disruptions pretty much continuously

+

We were a bit surprised when data showed that bounded election
still works better,
and even more suprised when the recursive version generated less instability than the classic one.

Our current explanation is that a continuous small readjustment of the
partition border induced by the classic version has more impact on the
total instability than rare larger disruptions,
however,
this needs further investigation by changing the partition size
and the movement speed of nodes

In conclusion

---

we introduced an algorithm for multi-leader election and network partitioning
that allows for explicit leader prioritization and arbitrary distance metrics,
and is proven to self-stabilise.

Data shows that it performs better than other algorithms in most scenarios

However, we know that we can build cases where the behaviour is particularly bad,
although, so far, it seems unlikely that they occur, we had to build them explicitly in simulation

The next steps

+

involve more testing,
we want to better understand when to use bounded election and when
to use something else, such as the classic sparse-choice

we want to see how frequently the corner cases happen,
and possibly find strategies
(for instance based on dynamic changes to priority) to tackle them

we believe that bounded election is sufficiently flexible
(due to the possibility to arbitrary change priorities and metrics)
and self stabilising,
it could be the building block of a new family of self-stabilising leader-election algorithms

This concludes the talk,
and since my guess is that if you are watching this I'm not there to answer questions,
then please forward them to my mailbox

Bye bye
