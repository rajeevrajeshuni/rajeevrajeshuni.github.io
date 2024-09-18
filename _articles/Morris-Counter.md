---
id: 1
title: "Introduction to Morris Counter"
subtitle: "Introduction to Morris Counter"
date: "14.08.2024"
tags: "Morris Counter"
---

# Introduction to Morris Counter

Morris counter is a probabilistic algorithm for approximate counting of a large number of events using a small amount of memory.

Let us say the actual number of events that happened is `n`. If we are using exact counting, to count `n` events, we will require `log n` bits, however the Morris counter does it with `log(log n)` bits.

The core idea is that we store a number `v`, which we will use to give an estimated value for the total number of events. Let us call the estimated number `m`.

The Morris counter formula to calculate v:

```
v = log₂(n + 1)
```

Extending the above formula we can find the estimated value:

```
m = 2ᵛ - 1
```

The challenge with the above equation is that we are not storing `n`. So we have to find another way to find the value of `v` without having value `n`. We use probability to solve this problem and calculate the value of `v`.

If we increment the value `v`, then the value of `m` will be:

```
m₁ = 2^(v+1) - 1
```

So we need to increment the variable `v` only once for `m₁ - m` events. So every time an event occurs we increment the value of v by probably `1/(m₁ - m)`. In this case, `v` should approximately converge to the function `ln(n+1)`.

Note that the probability of incrementing variable `v` depends on its current value.

## Use Cases

1. **Network Traffic Analysis**: Morris counters are useful for estimating network traffic, especially when there are a large number of packets or connections.

2. **Distributed Systems**: Maintaining event counts across many nodes in distributed systems can be prohibitively memory-intensive. Morris counters can reduce this cost by providing a probabilistic count.