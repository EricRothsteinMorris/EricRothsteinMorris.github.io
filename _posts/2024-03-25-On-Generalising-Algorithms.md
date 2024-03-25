---
layout: post
title: On Generalising Algorithms: A LeetCode Example
subtitle: From Linked Lists to Automata
tags: [LeetCode, Automata]
comments: true
---
## Introduction
Maybe some of you have tried to solve [LeetCode 1171: Remove Zero Sum Consecutive Nodes from Linked List](https://leetcode.com/problems/remove-zero-sum-consecutive-nodes-from-linked-list/). For those of you who have not, the problem is the following: given the `head` of a linked list of integer numbers, delete all subsequences of numbers that add up to zero until no such subsequences remain. So, for example, the list 
\[1\rightarrow 2 \rightarrow -3 \rightarrow 3 \rightarrow 1 \] can reduce to either \(3\rightarrow 1\) or to \(1\rightarrow 2 \rightarrow 1\). The constraints are that the list contains between 1 and 1000 nodes and that each element of the list is in the range \([-1000, 1000]\).

The problem is listed as *medium* difficulty (probably because the hints are misleading). In my opinion, the key observations here are that the sum the elements of the input list is the same as the sum of the elements of the output list, and that the output list is embedded in the input list. 

So what does this have to do with automata at all? Well, whenever an input sequence `i` takes you to a node `n` and a prefix `p` of `i` takes you to the same node `n`, then there is definitely a loop in the automaton which is explored by  the suffix after `p`. We cam nicely describe these behaviours using *causal functions*. 

Given a set of inputs \(I\) and a set of outcomes \(O\), a causal function \(f\) is a function of type \(I^*\rightarrow O\). For this problem, we set \(I=[-1000, 1000]\), \(O=\mathbb{Z}\), and \(f=\sum\), with \(\sum: I^*\rightarrow O\) defined, for \(n \in I\) and \(s,s' \in I^*\), by: 
\[
\sum(s)=
\begin{cases}
i + \sum(s') & \quad \text{when $s = s'\cdot i$};\\ 
0 & \quad \text{otherwise}.
\end{cases}
\]
