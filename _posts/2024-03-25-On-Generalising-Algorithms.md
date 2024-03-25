---
layout: post
title: On Generalising Algorithms - A LeetCode Example
subtitle: From Linked Lists to Automata
tags: [LeetCode, Automata]
comments: true
---
Maybe some of you have tried to solve [LeetCode 1171: Remove Zero Sum Consecutive Nodes from Linked List](https://leetcode.com/problems/remove-zero-sum-consecutive-nodes-from-linked-list/). For those of you who have not, the problem is the following: given the `head` of a linked list of integer numbers, delete all subsequences of numbers that add up to zero until no such subsequences remain. So, for example, the list 
\\[1\rightarrow 2 \rightarrow -3 \rightarrow 3 \rightarrow 1\\] can reduce to either \\(3\rightarrow 1\\) or to \\(1\rightarrow 2 \rightarrow 1\\). The constraints are that the list contains between 1 and 1000 nodes and that each element of the list is in the range \\([-1000, 1000]\\).

The problem is listed as *medium* difficulty (probably because the hints are misleading). In my opinion, the key observations here are that the sum the elements of the input list is the same as the sum of the elements of the output list, and that the output list is embedded in the input list. 

So what does this have to do with automata at all? Well, whenever an input sequence `i` takes you to a node `n` and a prefix `p` of `i` takes you to the same node `n`, then there is definitely a loop in the automaton which is explored by  the suffix after `p`. We cam nicely describe these behaviours using *causal functions*. 

Given a set of inputs \\(I\\) and a set of outcomes \\(O\\), a causal function \\(f\\) is a function of type \\(I^* \rightarrow O\\). For this problem, we set \\(I=[-1000, 1000]\\), \\(O=\mathbb{Z}\\), and \\(f=\sum\\), with \\(\sum : I^* \rightarrow O\\) defined, for \\(n \in I\\) and \\(s,s' \in I^*\\), by: 

$$
\sum(s)=
\begin{cases}
i + \sum(s') & \quad \text{when } s = s'\cdot i;\\ 
0 & \quad \text{otherwise}.
\end{cases}
$$

Now, consider an automaton whose set of states is \\(\mathbb{Z}\\), its initial state is 0 and the resulting state starting on \\(x\\) for the input \\(i\\) is the state \\(x+i\\). For this automaton, after consuming the sequence \\(s\\), we finish on \\(\sum(s)\\). Now,  if \\(s=xyz\\) such that \\(\sum(y)=0\\) then \\(\sum(s) = \sum(xz)\\), and the sequence \\(y\\) loops \\(\sum(x)\\) to itself. 

We now consider one of the best solutions for this problem (**SPOILER ALERT**: if you have not solved it, try it yourself, is quite a fun problem!).
<details>
  <summary>Click here to see the Solution</summary>


```python
    def removeZeroSumSublists(self, head: Optional[ListNode]) -> Optional[ListNode]:
        # init is a ListNode whose value is 0 and has head as its next element. 
        # It helps us in case the whole head adds up to zero
        init = ListNode(0, head) 
        # This hashmap uses sum values as keys and nodes as values
        prefix_sums = {0: init}
        # Calculate the prefix sum for each node and add to the hashmap
        # Duplicate prefix sum values will be replaced
        prefix_sum = 0
        current = init
        while current:
            prefix_sum += current.val
            # Important: we update a value only if we found a prefix p and a prefix pq such that sum(q)=0
            prefix_sums[prefix_sum] = current
            current = current.next
        # Reset prefix sum and current
        prefix_sum = 0
        current = init
        # Delete zero sum consecutive sequences by setting node before sequence to node after
        while current:
            prefix_sum += current.val
            # We are at state prefix_sum, do we know a longer sequence that takes us here? 
            # If we do, then we connect to the suffix of that longer sequence
            current.next = prefix_sums[prefix_sum].next
            current = current.next
        return init.next
```


</details>


<details>
  <summary>Click here to see the Generalisation</summary>


```python
    def sequenceMinimisation(self, list: Optional[ListNode]) -> Optional[ListNode]:
        prefix_key = sum([])
        # This entry helps us in case the sequence is equivalent to the empty sequence
        init = ListNode(prefix_key, list) # This is a value
        prefix = []
        current = init
        prefixes = {prefix_key: current}
        while current:
            prefix.append(current.val)
            prefix_key = sum(prefix)  
            prefixes[prefix_key] = current
            current = current.next
        # Reset prefix_key sum and current
        prefix = []
        current = init
        # Delete zero sum consecutive sequences by setting node before sequence to node after
        while current:
            prefix.append(current.val)
            prefix_key = sum(prefix)  # it is possible that prefix_key is an acc so this could be +=
            current.next = prefixes[prefix_key].next
            current = current.next
        return init.next
```


</details>

TEST TO SEE IF CODE WORKS OUTSIDE OF SPOILER BLOCKS:
```python
    def removeZeroSumSublists(self, head: Optional[ListNode]) -> Optional[ListNode]:
        # init is a ListNode whose value is 0 and has head as its next element. 
        # It helps us in case the whole head adds up to zero
        init = ListNode(0, head) 
        # This hashmap uses sum values as keys and nodes as values
        prefix_sums = {0: init}
        # Calculate the prefix sum for each node and add to the hashmap
        # Duplicate prefix sum values will be replaced
        prefix_sum = 0
        current = init
        while current:
            prefix_sum += current.val
            # Important: we update a value only if we found a prefix p and a prefix pq such that sum(q)=0
            prefix_sums[prefix_sum] = current
            current = current.next
        # Reset prefix sum and current
        prefix_sum = 0
        current = init
        # Delete zero sum consecutive sequences by setting node before sequence to node after
        while current:
            prefix_sum += current.val
            # We are at state prefix_sum, do we know a longer sequence that takes us here? 
            # If we do, then we connect to the suffix of that longer sequence
            current.next = prefix_sums[prefix_sum].next
            current = current.next
        return init.next
```





