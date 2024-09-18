---
id: 2
title: "Stable Marriage Problem"
subtitle: "Stable Marriage Problem"
date: "2024.12.07"
tags: "tag1, tag2"
---

# Stable Marriage Problem

The stable marriage problem deals with optimal matching of a list of men and women, so that all marriages are stable.

To give an example, let us say we have a list of men [m₁, m₂, ..., mₙ] and women [w₁, w₂, ..., wₙ] and each man has a list of preferences for the women they want to marry and each woman also has a list of preferences for the men they want to marry.

A marriage is considered unstable when two people of opposite genders prefer each other over their current partner. So if we match two couples (mₓ, wₓ) and (mᵧ, wᵧ), the marriage is considered unstable when mₓ prefers wᵧ over wₓ and wᵧ also prefers mₓ over mᵧ.

The stable marriage problem is solved by the Gale-Shapley algorithm which gives stable allocation to any list of men and women of the same length.

## Algorithm:

1. Each man or woman can be in a **free** or **engaged** state. Initially all men and women will be considered **free**.

2. Each man and woman has a list of preferred partners of the opposite gender. Each person's preference list contains the list of all the people of opposite gender in the order of preference.

3. In each round each **free** man proposes the most preferred women whom he has not previously proposed to irrespective of the state of the women. In each round, each man proposes only one woman.

4. If a woman is **free**, she picks the most preferred man from the list of prospects and both the man and woman are now considered **engaged**.

5. If a woman is **engaged**, then the woman can break the engagement if she receives a proposal from a man whom she prefers more than her current partner. In this case the man is going back to being **free**.

6. The cycle continues until all of them are **engaged**.
   a. Once the cycle ends, all **engaged** couples are considered married.

## Correctness of the algorithm:

Let us try to prove this algorithm by negation. Let us assume the algorithm gives us two couples (mₓ, wₓ) and (mᵧ, wᵧ) where mₓ prefers wᵧ over wₓ and wᵧ prefers mₓ over mᵧ. So both marriages are unstable.

Since all men propose in the order of their preference, mₓ would've proposed to wᵧ before wₓ. Given that mₓ is married to wₓ the only way this is possible is that wᵧ has rejected mₓ's proposal.

Since wᵧ has rejected the mₓ proposal, this means that:

1. wᵧ was already engaged to another man mz who wᵧ prefers more than mₓ.

2. Since wᵧ married mᵧ this means that either mz is the same as mᵧ or wᵧ prefers mᵧ more than mz.

3. In both of these cases, wᵧ prefers mᵧ more than mₓ. So the initial scenario is not even possible if we follow the algorithm.

## Time and Space Complexity:

Since each man proposes to each woman only once, each man can propose to maximum n women. So the maximum number of proposals will be O(n²). So the worst case time complexity is O(n²), assuming in each round the women decide on the proposals in constant time.

For space complexity, we have to save preferences for men and women, which is O(n²) and the saving of engagements will take O(n) space. So space complexity is also O(n²).

## Use cases:

1. Akamai CDN uses a generalized version of the Gale-Shapley algorithm to match clients to their edge servers. Check out this link.

2. The algorithm was also used to match students to schools, medical interns to hospitals, and match organ donors to patients.

## Notes:

Lloyd Shapley and Alvin E. Roth won the Nobel Prize in Economics for their work on stable allocations.