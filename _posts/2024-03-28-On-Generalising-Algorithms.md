---
layout: post
title: On Generalising Algorithms - A LeetCode Example
subtitle: Creating Higher-Order Functions
tags: [LeetCode, Higher-Order Functions]
comments: true
---
Maybe some of you have tried to solve [LeetCode 1171: Remove Zero Sum Consecutive Nodes from Linked List](https://leetcode.com/problems/remove-zero-sum-consecutive-nodes-from-linked-list/). For those of you who have not, the problem is the following: given the `head` of a linked list of integer numbers, delete all subsequences of numbers that add up to zero until no such subsequences remain. So, for example, the list 
\\[1\rightarrow 2 \rightarrow -3 \rightarrow 3 \rightarrow 1\\] can reduce to either \\(3\rightarrow 1\\) or to \\(1\rightarrow 2 \rightarrow 1\\).

Let us review one of the best solutions for this problem (**SPOILER ALERT**: if you have not solved it, try it yourself, is quite a fun problem!).

```python
def removeZeroSumSublists(self, head: Optional[ListNode]) => Optional[ListNode]:
    init = ListNode(0, head) 
    prefix_sums = {0: init}
    prefix_sum = 0
    current = init
    while current:
        prefix_sum += current.val
        prefix_sums[prefix_sum] = current
        current = current.next
    prefix_sum = 0
    current = init
    while current:
        prefix_sum += current.val
        current.next = prefix_sums[prefix_sum].next
        current = current.next
    return init.next
```

So, why does this blog post mention higher-order functions? To get to that point, we first need to talk about *causal functions*. Given a set of inputs \\(I \\) and a set of outputs \\(O \\), a *causal function* \\(f \\) is a function of type \\(I^* \rightarrow O\\), i.e., \\(f \\) receives a sequence of inputs in \\(I^* \\) and produces some output in \\(O \\). For this problem, we consider the sum causal function 
\\(\sum : \mathbb{Z} ^* \rightarrow \mathbb{Z} \\), defined, for \\(n \in \mathbb{Z} \\) and \\(s,s' \in \mathbb{Z}^* \\), by: 

$$
\sum(s)=
\begin{cases}
\sum(s') + i & \quad \text{when } s = s'\cdot i  ;\\ 
0 & \quad \text{otherwise}.
\end{cases}
$$

Note that for a sequence \\(s=xyz\\) where \\(\sum(y)=0\\), we know that \\(\sum(s) = \sum(xz)\\), so we could remove the sub-sequence \\(y\\), but the key point here is that if we apply \\(\sum \\) to all the prefixes of \\(s\\), we would "repeat" value of \\(\sum(x)\\) twice: for the prefix \\(x\\) and for \\(xy\\). We formalise this concept of "repeating" with the *trace*. Given a causal function 
\\(f : I^* \rightarrow O \\), sequences \\(s,s' \in I^* \\) and \\(i \in I\\), we define the *trace of \\(s\\) under \\(f\\)*, denoted 
$$ [\![ s ]\!]_f $$, by

$$
[\![ s ]\!]_f=
\begin{cases}
[\![ s' ]\!]_f \cdot f(s) & \quad \text{when } s =  s'\cdot i ;\\ 
\varepsilon & \quad \text{otherwise}.
\end{cases}
$$

( \\(\varepsilon\\) denotes the empty sequence. )

Intuitively, we say that there is a repeated value in the trace iff there are indices \\(i \\) and \\(j \\) with \\(i \< j\\) such that $$ [\![ s ]\!]_f[i] $$ is equal to $$ [\![ s ]\!]_f[j] $$. For example, the sequence `[1 -> 2 -> 3 -> -3 -> 4]` compresses to `[1 -> 2 -> 4]` because its trace under `sum` is `[1, 3, 6, 3, 4]`, and the value `3` is repeated, meaning that `[3, 6, 3]` that can be compressed to `[3]`, yielding the reduced trace `[1, 3, 4]` (the trace of `[1 -> 2 -> 4]`).

Consider now the following generalisation of the algorithm above that now takes a causal function as an input parameter.

```python
def sequenceCompression(self, head: Optional[ListNode], causal_function) => Optional[ListNode]:
    prefix_key = causal_function([])
    init = ListNode(prefix_key, head)
    prefix = []
    current = init
    prefixes = {prefix_key: current}
    while current:
        prefix.append(current.val)
        prefix_key = causal_function(prefix)  
        prefixes[prefix_key] = current
        current = current.next
    prefix = []
    current = init
    while current:
        prefix.append(current.val)
        prefix_key = causal_function(prefix)
        current.next = prefixes[prefix_key].next
        current = current.next
    return init.next
```

where `causal_function` is a causal function with hashable output. This algorithm is able to compress a list `L` to a sub-list `l` such that `causal_function(L)` is equal to `causal_function(l)`, but note that compression only happens if there is a repeated element in the trace of `L` under `causal_function`. For example, consider the `median` function, we obtain the following compressions:

```python
[1 -> 2 -> 3 -> -3 -> 4] => [1 -> 2 -> 4]           (median is 2)
[1 -> 2 -> 3 -> -3 -> -3] => [1]                    (median is 1)
[1 -> 2 -> 3 -> -6 -> 4] => [1 -> 2 -> 4]           (median is 2)
[0] => [0]                                          (median is 0)
[1 -> 2 -> 2 -> -2 -> -6] => [1]                    (median is 1)
[1 -> 2 -> 3 -> 4 -> 6] => [1 -> 2 -> 3 -> 4 -> 6]  (median is 3)
```
The sequence `[1 -> 2 -> 3 -> -3 -> -3]` compresses to `[1]` because its trace under `median` is `[1, 1.5, 2, 1.5, 1]`, and `1` repeats, yielding `[1]` (the trace of `[1]`). The reason why `[1 -> 2 -> 3 -> 4 -> 6]` does not compress to `[3]` is because its trace `[1, 1.5, 2, 2.5, 3]` has no repeated elements. Pretty neat, huh?

This algorithm runs in `O(n)` and uses `O(n)` space (for `prefix` and `prefixes`), and only requires the modification of the causal function for it to change its behaviour, hence its higher-order nature. These (latent) behaviours that appear when we change a parameter are what I studied very closely during my doctorate. Maybe I will write about those in the near future.

Thanks for reading!