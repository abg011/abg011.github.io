---
layout: post
title: "Consistent Hashing and More"
date: 2026-05-16
tags: [distributed-systems, system-design]
---

# Consistent Hashing, the Karger Paper, and Modern Distributed Systems — Q&A Notes

## Original Paper

- Consistent Hashing and Random Trees (Karger et al., 1997)
- Link: https://www.cs.princeton.edu/courses/archive/fall09/cos518/papers/chash.pdf

---

## Q: Origin server will always be a bottleneck if distinct documents are asked for, isn't it?

Yes.

Caching only helps when there is reuse.

If requests are mostly unique:

```text
cache hit rate -> very low
```

then every request eventually reaches the origin.

Caching optimizes repetition, not uniqueness.

---

## Q: How does consistent hashing solve this?

It does not solve uniqueness itself.

It solves:

```text
how to distribute ownership/routing cleanly across many nodes
```

Instead of hierarchical cache trees:

```text
Client -> Local -> Regional -> Root
```

consistent hashing distributes requests horizontally:

```text
key -> hash -> responsible node
```

This removes centralized bottlenecks.

---

## Q: What about the Plaxton/Rajaraman algorithm?

Plaxton/Rajaraman introduced distributed routing through a namespace instead of a strict hierarchy.

Requests progressively move closer to the object’s “home.”

Along the path:
- intermediate caches can store copies
- future requests terminate earlier
- hotspots diffuse naturally

This influenced:
- Pastry
- Tapestry
- DHTs
- CDN routing ideas

---

## Q: What are spread and load in consistent hashing?

### Spread

Spread measures:

> how many different nodes different clients may map the SAME key to

under inconsistent membership views.

### Load

Load measures:

> how unevenly objects/requests distribute across nodes

Low load means fewer hotspots.

---

## Q: What is the client/server in this context?

### Client
The machine/library making routing decisions.

### Server
The storage/cache nodes.

Example in Cassandra:

```text
Application + driver = client
Cassandra nodes = servers
```

---

## Q: How does Aerospike differ from Cassandra?

Aerospike uses:

```text
key -> fixed partition
partition -> node
```

instead of classic ring-style consistent hashing.

Partitions are fixed (typically 4096).

Ownership metadata is explicit.

---

## Q: Why did industry move away from pure hash rings?

Because operational issues emerged:
- vnode tuning
- token imbalance
- repair complexity
- hotspot management
- difficult debugging

Modern systems often prefer:
- partition maps
- explicit ownership tables
- metadata coordinators

---

## Q: Does the client look up multiple nodes?

Usually no.

Normally:

```text
key -> deterministic node
```

Replication may add fallback nodes, but routing is deterministic.

---

## Q: What if the chosen node doesn’t have the key?

Possible reasons:
- cache miss
- stale topology
- migration in progress
- replication lag

Systems may:
- retry another replica
- refresh metadata
- redirect requests

---

## Q: What happens if client metadata is stale during rebalance?

Example:

```text
x moved from C3 -> C4
```

but client still thinks:

```text
x -> C3
```

Possible behaviors:
- redirect
- forwarding
- metadata refresh
- temporary overlapping ownership

---

## Q: How does replication complicate things?

Now the question becomes:

```text
Which replicas own x right now?
```

instead of:

```text
Where does x live?
```

This introduces:
- repair
- quorum logic
- reconciliation
- ownership transitions

---

## Q: How does Cassandra solve this?

Cassandra chooses:

```text
availability > temporary consistency
```

Uses:
- gossip
- hinted handoff
- anti-entropy repair
- overlapping ownership

---

## Q: How does Aerospike rebalance?

Aerospike migrates at:

```text
partition granularity
```

not per-key granularity.

Only partition ownership metadata changes.

---

## Q: Can Aerospike get hot partitions?

Yes.

Hashing balances:

```text
keys
```

not necessarily:
- traffic
- bytes
- CPU
- IO

---

## Q: Can partitions become unevenly large?

Yes.

Hashing balances approximately:

```text
number of keys
```

not record sizes.

Large objects can create storage skew.

---

## Q: Is the ring even mentioned in the Karger paper?

Not really.

The paper is more abstract and probabilistic.

The modern “hash ring” became popular later as an implementation and visualization strategy.

---

## Q: Why did the paper use multiple hash functions?

The hash family mainly supported:
- randomized placement
- probabilistic guarantees
- spread/load analysis
- overlap under inconsistent views

The final mapping still resolved to:

```text
one key -> one selected bucket
```

---

## Q: If we want low spread, why not use a single hash?

A single hash works well only if:

```text
all clients see identical membership
```

Under inconsistent views:
- small topology differences can cause large mapping divergence

Hash families create probabilistic overlap between client views.

---

## Q: Are the hash functions tuned dynamically?

Usually no.

They are generally:
- fixed
- deterministic
- pseudo-random
- globally agreed upon

The system relies on probabilistic guarantees.

---

## Q: How does rebalance work when a new node is added?

Only nearby ownership changes.

Example:

```text
(100,300] moves from C2 -> C4
```

All other keys remain untouched.

---

## Q: What happens when a node goes down?

Its ownership range is inherited by the next node clockwise.

Example:

```text
C2 dies
C3 inherits C2's interval
```

---

## Q: If there is no replication and a node dies, is there data loss?

Yes.

Consistent hashing solves:
- routing
- placement
- smooth rebalance

It does NOT solve:
- durability
- fault tolerance
- availability

Without replication:

```text
node death = permanent data loss
```

---

## Q: What are virtual nodes (vnodes)?

Instead of:

```text
1 physical node = 1 position
```

use:

```text
1 physical node = many virtual positions
```

This smooths distribution and reduces imbalance.

---

## Deepest Takeaway

The real core idea is:

> small topology changes should cause small ownership changes.

The ring is only one implementation strategy.
