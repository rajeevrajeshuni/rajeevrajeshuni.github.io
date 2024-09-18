---
id: 0
title: "Introduction to Bloom Filters"
subtitle: "Introduction to Bloom Filters"
date: "08.12.2024"
tags: "Bloom Filters"
---

# Introduction to Bloom Filters

Bloom filter is a probabilistic data structure used to solve the set membership problem. A relaxed constraint here is that false positives are tolerated to an acceptable level.

Bloom filter uses a bit array to store the presence of items. Bloom filter uses hash functions to save the presence of an item without saving the actual item.

Bloom filters can give false positives, but never false negatives. This means that if the bloom filter returns positive, it means that the element may be present in the set, but if it returns negative, it means that the element is definitely not present in the set.

Now let us define a few variables before jumping into the algorithm:

1. `n` - The number of elements to be added in to the set.
2. `k` - The number of hash functions we use.
3. `m` - The length of the bit array we will use.

Let us assume we have `n` elements to insert into the set. Before inserting elements, let us create a bit array of size `m` with all values set to `0`, and `k` hash functions which return values in the range of `(0,m-1)`.

## Operation on the bloom filter:

### Steps for adding an element:

For each element we run `k` hash functions and then use the result as the index in the bit array. We mark the element at that index as `1`. We will also store the elements in secondary storage for bookkeeping.

```go
func addElement(item SetItem) {
    // Iterate over the array of hash functions
    for _, hashFunction := range hashFunctions {
        v := hashFunction(item)
        bitarray[v] = 1
    }
    // Here you'd save the item to secondary storage.
}
```

### Steps for looking up an element:

Similar to above, we run `k` hash functions on an element and use the result as an index in the bit array. We check the value in each of those indexes and if the value is `1` in all of them, the bloom filter returns true.

```go
func lookUpElement(item SetItem) bool {
    // Iterating over the array of hash functions
    for _, hashFunction := range hashFunctions {
        v := hashFunction(item)
        if bitarray[v] == 0 {
            return false
        }
    }
    return true
}
```

The bits at those indexes could be made `1` by different elements resulting in a false positive, but that is a tradeoff with bloom filter.

### Steps for deleting an element:

You cannot delete an element from the bloom filter. This is a significant drawback, but that is the tradeoff for reducing space utilization.

## Example:

We choose three hash functions and a bit array of size `16` with all bits as `0`. Let us add elements `A` and `B`.

Flow for adding the elements:

We take the element and get the has value from the three functions and mark the corresponding bits as `1` in the bit array.

Let the hash values for `A` be 7, 1 and 14. So the array will change to:

| Values | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 |
|--------|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Index  | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10| 11| 12| 13| 14| 15|

Let the hash values for `B` be 8, 1 and 15. So the array will change to:

| Values | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 1 | 1 | 0 | 0 | 0 | 0 | 0 | 1 | 1 |
|--------|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Index  | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10| 11| 12| 13| 14| 15|

Flow for looking up an item:

Let's say we are searching for the element `C`, whose hash values are 1,15 and 9. The values in the bit array at index 1,15 are 1, but the value at index 9 is 0. So we can safely say that the `C` is not part of the set.

> 💡 When hashing, why do we usually take array lengths in powers of 2?
> 
> This is because, usually we do a mod of the size of the array to find the index. If the array size is a power of 2, then taking a mod is simply a bitwise operation. Let's say the number is 254 which is 11111110. 254%16 will be the last 4 bits which is 1110 which is 14.

## Advantages of a bloom filter over a set data structure:

1. Optimized space usage:
   a. Usually a set data structure is maintained using a self-balancing binary tree, hash table, array, linked list or trie.
   b. These data structures require storing at least the elements themselves (except for trie). The items could be a small integer or a large string.
   c. In contrast, the bloom filter takes the same amount of space for an item of any length.

2. Constant insertion and lookup time:
   a. Bloom filters also have the advantage that the time taken to add an item does not depend on the existing elements already added. It is a constant value O(k). The same is the case for lookup time.

3. There is also the advantage of parallelizing the execution of hash functions.

## Choosing a hash function:

1. Let us detail our requirements for the hash function
   a. Fast computation
   b. Uniform distribution
   c. Cryptographic security to avoid reverse engineering is not a concern here.

2. In line with the above requirements, bloom filters typically use the hash functions MurmurHash, JenkinsHash, and FNV Hash.

## Tuning a bloom filter:

We can intuitively see that if we keep adding elements to a bloom filter, the collisions increase and hence the false positive rate also increases.

In the worst case, the bloom filter returns true for any given element. Since we have all the elements saved in secondary storage, we need to increase the values `m` and `k` and create another bloom filter.

However how do we know the optimal values of `m` and `k`, given `n` and an error rate? The exact math is outside the scope of this blog, but you can explore https://hur.st/bloomfilter/ to get these values.

## Example use cases:

1. Akamai uses a bloom filter to avoid caching one-hit wonders. They cache an item only when it is accessed for the second time.

2. Databases such as Google Bigtable, Apache Cassandra use bloom filters to reduce disk lookups for non-existent items.