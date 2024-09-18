---
id: 3
title: "Consistent Hashing"
subtitle: "Consistent Hashing"
date: "2024.12.06"
tags: "Consistent Hashing"
---

# Consistent Hashing

Consistent Hashing is a form of hash-based partitioning which provides an elastic way to add/remove servers in a distributed system with minimal data movement across servers. For us to appreciate consistent hashing, let us start with a basic hash partitioning approach.

## Simple Hash-Based Partitioning:

Let us consider a scenario where we have to map k objects to n servers.

1. Let us say we use a hash function h(x) for the object x:

   ```
   h(x) = ((ax + b) mod P) mod n
   ```

2. In the above equation P is a prime number such that P ≥ n and a is chosen randomly in the range [1, P - 1] and b is chosen randomly in the range [0, P - 1].

We number each server from 0 to n - 1, and we assign each object to the h(x) server.

### Problem with the above approach:

In case of a server failure, all objects mapped to that server should be remapped to other servers. However, the hash function defined above does not give us any straightforward way to remap the objects. If we remap all the objects of this server to another server, that single server will hold double the objects compared to other servers. Otherwise, if we renumber the servers, we end up rehashing all the objects.

## Consistent Hashing:

With consistent hashing rather than simplistic partitioning, both objects and servers are first mapped to a point on the circle. Each object is assigned to the next server which appears in the circle while moving clockwise.

Let us look at the example image below where servers S1, S2, S3 and objects O1, O2, O3 are mapped to a circle.

[Image description: A circle with points representing servers S1, S2, S3 and objects O1, O2, O3, O4, O5]

In the above case we map O1 to S1, O2, O3 to S2 and O4, O5 to S3. If a server fails, all the objects for that server are mapped to the next server that appears in a clockwise fashion. When a server is added, all the objects before it in the circle are remapped to it.

An improvement over this is that, rather than mapping actual servers to the circle we create multiple virtual servers for each server and map them on to the circle. Virtual servers are only logical entities. It is the same as having multiple instances of the same server in the circle. However by splitting the server into multiple virtual servers the objects are more evenly distributed across all the servers.

In the above example VS1, VS2, VS3 denote virtual servers for servers S1, S2 and S3. As we can see, O1 will be mapped to VS1, so it is essentially mapped to S1.

When a server is unhealthy, we remove all the corresponding virtual servers from the circle and remap the elements belonging to this server. When a server is added, we add multiple virtual servers to the circle and remap the relevant objects.

As a further extension, we can map an object to multiple virtual servers on the circle, thus other servers will act as replicas of the object. This will help us balance the load on the servers holding the most popular objects.