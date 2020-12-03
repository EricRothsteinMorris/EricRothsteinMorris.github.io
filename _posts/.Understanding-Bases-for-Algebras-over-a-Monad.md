---
layout: post
title: Understanding "Bases for Algebras over a Monad"
subtitle: Reflections on the work by Zetzsche, Silva, and Sammartino
tags: [Coalgebric Method]
comments: true
---
This is part of a series of blog posts on the coalgebraic method. I provide my own interpretation of the results presented in the paper to digest the information better and truly learn what was written.

## A First Impression
From the title, since monads model computational side effects, I could speculate that we could get nice guidelines about how to design APIs for monads. Let us see if this theory holds...

## Diving into the Paper
Let us start breaking down what they say:
> A monad may be seen as a generalisation of closure operators on partially ordered sets, and an algebra over a monad may be viewed as a set with an operation that allows the interpretation of formal linear combinations in a way that is coherent with the monad structure

There is a lot to unpack here. Let us first start with
> A monad may be seen as a generalisation of closure operators on partially ordered sets

So, a Partially Ordered Set (POSet) is a set \\(X\\) together with a partial order \\(\leq\subseteq X\times X\\) such that \\(\leq\\) is reflexive, transitive and antisymmetric, and a closure operator \\(cl:X\rightarrow X\\) satisfies the following properties.
  * \\(x\leq cl(x)\\) so, the repeated application of the closure operator brings you closer to a maximum
  * \\(x\leq y \Rightarrow cl(x)\leq cl(y)\\) so the closure operator preserves the partial order (it lifts)
  * \\(cl(cl(x))= cl(x)\\) so the closure operator is idempotent.

I can see how monads generalise this concept:
  * the unit of the monad \\(T\\), that is \\(X\xrightarrow{\eta} T(X)\\), generalises \\(x\leq cl(x)\\)
  * The functorial nature of \\(T\\), that is\\(X\xrightarrow{f} Y \mapsto T(X)\xrightarrow{T(f)} T(Y)\\), generalises \\(x\leq y \Rightarrow cl(x)\leq cl(y)\\)
  * the multiplication of the monad \\(T\\), that is \\(T(T(X))\xrightarrow{\mu} T(X)\\), generalises \\(cl(cl(x))= cl(x)\\)

The next part
> An algebra over a monad may be viewed as a set with an operation that allows the interpretation of formal linear combinations in a way that is coherent with the monad structure

To understand this part, I think it is useful to check Bartoz's blog post on [Algebras for Monads](https://bartoszmilewski.com/2017/03/14/algebras-for-monads/) (you might want to read his previous wonderful post on F-algebras too!).

I like to think of monads as containers of data. TODO explain what a monad is

First, an algebra over a monad \\(T\\) is a tuple \\((X,h)\\) consisting of an object \\(X\\) and a morphism \\(h\colon T(X)\rightarrow X\\) that satisfies the following properties:

(**Note**: I still need to learn how to include commutative diagrams in the webpage, you'll have to deal with equations for now. Sorry!)

$$X \xrightarrow{id_X}X =X\xrightarrow{\eta_X}T(X)\xrightarrow{h}X,\quad \text{(Unit triangle)}$$

so, if \\(\eta_X\\) naturally packs X into \\(T(X)\\), then \\(h\\) adequately unpacks \\(T(X)\\) back into \\(X\\), and

  $$T^2(X)\xrightarrow{\mu_X} T(X)\xrightarrow{h}X =T^2(X)\xrightarrow{T(h)} T(X)\xrightarrow{h}X,\quad \text{(Multiplication square)}$$

which I interpret as follows: to combine containers and then unpack the data is equivalent to lift the unpacking operation, and then unpacking normally.

Because algebras have a signature \\(h\colon T(X)\rightarrow X\\), I can only think that \\(T(X)\\) represents a formal linear combination, and its interpretation is an \\(X\\). The other option, although maybe less plausible, is that they mean the free algebra of the monad \\(T\\) and we use the T-algebra to interpret it.

The paper talks about the *free \\(k\\)-vector space monad*, an instance of the multiset monad over some semiring \\(S\\) when \\(S\\) is given by the field \\(k\\). It is a fancy way to say \\(T(X)=\texttt{Map}\ X\ S\\), and \\(\vec{v}:T(X)\\) is a *finitely-supported* map indexed by the elements in \\(X\\). Finite support means the following: saying that $$(x,0)\in \vec{v}$$ is equivalent to saying that $$x$$ is not in the map $$\vec{x}$$; in other words, the *default value* of $$\vec{v}[x]$$ is the 0 of the semiring $$S$$. This is consistent with the following notation from the paper: the equation $$\sum_{i=1}^{n}s_i\cdot x_i$$ represents the map $$\vec{v}=[(x_1,s_1),\ldots,(x_n,s_n)]$$.

The unit \\(\eta_X\\) maps \\(x\\) to \\([(x,1)]\\) where 1 is the unit of the semiring \\(S\\), and the multiplication \\(\mu_X\\) transforms a \\(\texttt{Map}\ (\texttt{Map}\ X\ S)\ S\\), say $$[(\phi_1,s_1),\ldots,(\phi_n,s_n)]$$, into the \\(\texttt{Map}\ X\ S\\) given by the weighted sum

$$\mu_X([(\phi_1,s_1),\ldots,(\phi_n,s_n)])[x]=\sum_{i=1}^ns_i\cdot\phi_i[x].$$

A concrete example for this monad would be with $$X=\{x,y,z\}$$ and $$S = (\mathbb{N},+,.,0,1)$$. Here, $$T(X)$$ is a discrete three-dimensional space, whose vectors are indexed by $$[x,y,z]$$, so $$[0,0,0]$$ is the default value, $$\eta_X(x)=[1,0,0]$$, $$\eta_X(y)=[0,1,0]$$ and $$\eta_X(z)=[0,0,1]$$, and the multiplication of $$[([1,0,0],1),([0,1,0],2),([0,0,1],3)]$$ is $$[1,2,3]$$.

Let us try to define a $$T$$-algebra $$T(X)\xrightarrow{h}X$$. This $$T$$-algebra is supposed to satisfy the unit triangle and the multiplication square. The paper suggests defining $$h$$ as

$$h([(x_1,s_1),(x_2,s_2),\ldots])\triangleq \sum_{i} s_i.x_i,$$

but here we face a complication: for $$X=\{x,y,z\}$$, the sum $$a.x + b.y + c.z$$ must be of type $$X$$. In other words, this definition requires $$X$$ to be a *vector space*.

**REMARK:** due to the conditions over $$T$$-algebras, it is not easy to define them over arbitrary sets.

Let us change $$X$$ so that it is now $$X=\mathbb{N}$$, so $$T(X)$$ is the set of infinite vectors but with finite support. These infinite vectors are indexed by natural numbers, i.e. $$[0,1,2,3,4,5,\ldots],$$ but they have a finite number of elements whose value is not zero. The unit triangle is satisfied in this case, and the for the multiplication square, consider the following: $$T(X)$$ is also a vector space, and the vector of vectors, $$[([s_0,s_1, \ldots],\alpha),([t_0,t_1, \ldots],\beta)]$$ maps to

$$\alpha.[s_0,s_1, \ldots]+ \beta.[t_0,t_1, \ldots],$$

via $$T(h)$$; now, since $$T(X)$$ is a vector field, the expression above is equal to

$$[(0,\alpha.s_0+\beta.t_0),(1,\alpha.s_1+\beta.t_1),\ldots],$$

which maps via $$h$$ to the natural number

$$0.(\alpha.s_0+\beta.t_0)+ 1.(\alpha.s_1+\beta.t_1)+\ldots+i.(\alpha.s_i+\beta.t_i) + \ldots.$$

For the other equality, $$[([s_0,s_1, \ldots],\alpha),([t_0,t_1, \ldots],\beta)]$$ maps to

$$[(0,\alpha.s_0+\beta.t_0),(1,\alpha.s_1+\beta.t_1),\ldots],$$

via $$\mu_X$$; which maps via $$h$$ to the natural number

$$0.(\alpha.s_0+\beta.t_0)+ 1.(\alpha.s_1+\beta.t_1)+\ldots+i.(\alpha.s_i+\beta.t_i) + \ldots$$

Distributive laws are one of the most interesting transformations. Intuitively, a distributive law states that manipulating data inside data structures is equivalent to manipulating the data and then storing it in data structures. Of course, we can always try to unpack data, manipulate it, and pack it again, but that does not sound very clean. Instead, we should dive into the data structure and the manipulate data without unpacking.

Given a monad $$(T,\mu,\eta)$$ and an endofunctor $$F$$, a distributive law $$\lambda\colon TF\Rightarrow FT$$ is a natural transformations that satisfies the following.

First, we require that

$$F(X)\xrightarrow{F(\eta_X)}FT(X)$$

is equal to

$$F(X)\xrightarrow{\eta_{F(X)}}TF(X)\xrightarrow{\lambda_{X}} FT(X),$$

and also that

$$T(TF)(X)\xrightarrow{T(\lambda_X)}(TF)T(X)\xrightarrow{\lambda_{T(X)}} F(TT)(X)\xrightarrow{F(\mu_X)}FT(X)$$

is equal to

$$(TT)F(X)\xrightarrow{\mu_{F(X)}}TF(X)\xrightarrow{\lambda_X} FT(X)$$

With these properties, any $$FT$$-coalgebra $$k\colon FT(X)$$ lifts to an $$F$$-coalgebra $$k^\#$$ in the category of $$T$$-algebras, with

 $$(T(X),\mu_X)\xrightarrow{k^\#}\left(F(T(X)), F(\mu_X)\circ\lambda_{T(X)}\right)$$

defined by

$$k^\#\triangleq \left(F(\mu_X) \circ \lambda_{T(X)}\right)\circ T(k)$$

where *behavioural equivalence* is preserved (see [this](https://homepages.cwi.nl/~janr/papers/files-of-papers/2013-Generalised-power-set-LMCS.pdf) wonderful paper by Silva, Bonchi, Bonsangue and Rutten for a more detailed explanation.)

## Generators for Algebras
OK, enough preliminaries. Now into the new stuff. A *generator* for a $$T$$-algebra $$(X,h)$$ is a tuple $$(Y,Y\xrightarrow{i}X,X\xrightarrow{d}T(Y))$$ such that $$X\xrightarrow{\texttt{id}_X}X=X\xrightarrow{d}T(Y) \xrightarrow{i^\#}X$$.

**Note:** the first thing you should notice is how the generator's condition is a generalisation of the unit triangle for $$T$$-algebras. This hints us in the direction of a notion of equivalence or equality for elements of $$X$$ which is given by $$i^\#$$ and $$d$$.

Based on this information, we shall name the function $$i$$ the *disguised algebra* and the function $$d$$ the *disguised unit*.

A morphism $$(Y_\alpha,i_\alpha,d_\alpha)\xrightarrow{f}(Y_\beta,i_\beta,d_\beta)$$ between generators $$(Y_\alpha,i_\alpha,d_\alpha)$$ and $$(Y_\beta,i_\beta,d_\beta)$$ for $$(X,h)$$ satisfies the following two properties:

$$ Y_\alpha \xrightarrow{f} Y_\beta \xrightarrow{i_\beta}X=Y_\alpha \xrightarrow{i_\alpha} X,$$

and

$$ X \xrightarrow{d_\alpha} T(Y_\alpha)\xrightarrow{T(f)}T(Y_\beta)=X\xrightarrow{d_\beta}T(Y_\beta).$$

Let
