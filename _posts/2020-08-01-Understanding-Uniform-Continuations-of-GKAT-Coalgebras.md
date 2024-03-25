---
layout: post
title: Understanding Uniform Continuations of GKAT coalgebras
subtitle:
tags: [Coalgebra,Programming]
comments: true
---
**Note**: This blog post is a note from me to me to try to understand the concept of continuations and well-nested GKAT coalgebras

# Definitions
## \\(G\\)-Coalgebras
The functor is \\(G(X)=(2+\Sigma\times X)^{At}\\). A *\\(G\\)-coalgebra* is a pair \\(\mathcal{X}=(X,\delta)\\) where \\(\delta^\mathcal{X}\\) is a function of type \\(X\rightarrow G(X)\\). We say that a state \\(x\in X\\) *terminates accepting* an atom \\(\alpha\in At\\) iff \\(\delta^{\mathcal{X}}(x)=1\\) and *terminates rejecting* \\(\alpha\\) iff \\(\delta^{\mathcal{X}}(x)=0\\). If the system does not terminate, then \\(\delta^{\mathcal{X}}(x)=(p,y)\\) where \\(p\\) is an action and \\(y\\) is a state. The action \\(p\\) represents the next available action; e.g., in a branching \\(p+_{b}q\\) if we receive \\(\alpha\\) such that \\(\alpha \Rightarrow b\\), then the action should be \\(p\\), otherwise, it should be \\(q\\).

The acceptance condition is the same as NDA.

## Pseudostates and Uniform Continuations
A *pseudostate* is a function of type \\(At\rightarrow 2+\Sigma\times X\\) (i.e. ''dynamics'' or ''local behaviour''). The pseudostate 1 is the constant map to 1. For \\(Y\subseteq X\\) and pseudostate \\(h\\), the uniform continuation of \\(Y\\) by \\(h\\) in \\(\mathcal{X}\\) is a \\(G\\)-coalgebra \\(\mathcal{X}[Y,h]\\) defined by \\((X,\delta[Y,h])\\) where \\(\delta[Y,h] (x) (\alpha)\\) is \\(h(\alpha)\\) if \\(x\\) would normally terminate and accept on \\(\alpha\\), otherwise it is \\(\delta(x)(\alpha)\\).

So far so good, not so hard.
## Solutions
A solution is a map from the state space of a coalgebra to GKAT expressions. The map intends to preserve semantics, so you map a state to an expression that has the same language of guarded strings as the state. What happens is more or less the following: a state \\(x\\) that reacts to \\(\alpha\\) with \\(\delta(x)(\alpha)=(p,y)\\) should map to an expression of the form \\(p\cdot\bullet +_{\alpha}\star \\) where \\(\star\\) covers the rest of the cases for the rest of the atoms and \\(\bullet\\) is the solution for \\(y\\).  
It may happen that, if several atoms have the same conditions, then they are grouped together. For example, \\(b\land c\\) and \\(b\land \bar{c}\\) can be grouped together by the conditional \\(b\\).

The good news is that branching expressions are equivalent to a wide range of other expressions. For example \\(g=e\cdot g +_{b}1\\) is equivalent to \\(e^{(b)}\\) if \\(e\\) is strictly productive.


## Well-nested coalgebras
A base case for a well-nested coalgebra is a coalgebra whose dynamics for every state is a function that immediately accepts/rejects an atom and terminates. This is ok, because it is related to determinacy (e.g. we know who's going to win a perfect information game even before playing.)
