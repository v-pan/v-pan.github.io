---
title: Manifolds
draft: false
---
Manifolds form "smooth looking" spaces, and so in some sense, "look locally Euclidean" in some consistent way.

Much of the benefit of differential geometry will be to piece together familiar structures (eg. derivatives, integrals, etc.) defined locally, such that we can use the "smoothness" of the manifold to define this structure everywhere. This then allows us to utilise these structures even when it might not be possible to write down a consistent set of coordinates over the entire space.

# Charts

## Chart
A *chart* on a set $M$ is a pair $(U, \varphi)$, $U \subset M$, where $\varphi \colon U \rightarrow \varphi(U) \subset \mathbb{R}^n$ bijective, and $\varphi(U)$ open.

In other words, a chart identifies a subset $U$ of $M$ to an open subset $\varphi(U)$ of $\mathbb{R}^n$. We call $\varphi^{-1}$ a *local parameterisation* of $M$, as it gives a set of "coordinates" in $R^n$ to a local neighbourhood of $M$.

Charts will act as local views into $M$ that let us look at $M$ as if it were indeed Euclidean space, making concrete this idea of a manifold "looking locally Euclidean". These views wouldn't be particularly useful if there was no way to move between them to get a full picture of $M$.

## Transition map
The *transition map* from a chart $(U, \varphi)$ to another chart $(W, \psi)$ is the map $\psi \circ \varphi^{-1}$, wherever this is defined.

In other words, to transition from one chart to another, we take where they overlap, undo one chart, and apply the other.

## Compatibility
Two charts are considered *compatible* if the transition map between them is either:
1. Defined nowhere
2. A diffeomorphism

Recall that:
1. A smooth map is infinitely continuously differentiable, i.e. an element of $C^{\infty}$ 
2. A diffeomorphism is a smoothly invertible map, i.e. a map $\varphi$ is a diffeomorphism if it is smooth, and if $\varphi^{-1}$ is well-defined and also smooth.

Diffeomorphisms are the structure preserving maps, i.e. isomorphisms, between differentiable manifolds.

Compatibility is required for the transition between charts to be smooth. This helps formalise the idea that manifolds are "smooth looking" spaces, since we will smoothly transition between different, also smooth, local views over $M$. On the whole, this will mean that compatible local views of $M$ preserve the same smoothness, which will allow us to define a global smooth structure on $M$ by composing these local views.

## Atlas
An *atlas* on a set $M$ is a collection of mutually compatible charts, whose domains cover $M$.

> [!example] Example: Projective Plane
> A projective space $\mathbb{KP}^n$ is the set of all lines passing through the origin of space $\mathbb{K}^{n+1}$, with $\mathbb{K}^{n+1}$ being an $(n + 1)$ dimensional vector space over the field $\mathbb{K}$.
> 
> For example, for the projective plane $\mathbb{RP}^2$, we start in $\mathbb{R}^3$, and consider all lines passing through the origin. It should make no difference if we only considered the unit vector along each of these lines, which makes this set of lines look like the unit sphere. There is, however, one issue, which is that for any unit vector $u$, $-u$ would lie on the same line, double counting each line in the space. This hints that the space is a little more complicated, and that we might have that opposite ends of the sphere must be considered the same.
> 
> More formally, let us show this equivalence using charts. Pick any plane $P$ that does not pass through the origin, for example, we'll take $P$ to be the plane where $x = 1$.
> 
> Any $p \in P$ corresponds to some $(1, y, z) \in \mathbb{R}^3$. We can then identify any line $l$ passing through $(1, y, z)$ with the point $p$.
> 
> Similarly, let $Q$ be the plane where $y = 1$, and identify lines through $(x, 1, z)$ with $q \in Q$.
> 
> We can construct a chart $\varphi$ with $P$, from the set of lines by taking $\varphi(l) = (y, z)$ when $l$ passes through the point $p = (1, y, z)$. Similarly, we can construct a different chart with $Q$, this time taking $\varphi(l) = (x, z)$ when $l$ passes through the point $p = (x, 1, z)$.
> 
> The transition maps are given simply by following the line $l$ from one plane to the other. The line $l$ is a set of vectors, and if it passes through $P$ at $(1, y, z)$, then we can say
> $$
> l = span{(1, y, z)} = t(1, y, z),\ \forall t \in \mathbb{R}
> $$
> 
> To find the corresponding point $(x', 1, z')$ on $Q$, we simply need to solve
> 
> $$
> (x', 1, z') = t(1, y, z) = (t, ty, tz)
> $$
> 
> so it must be that $t = \frac{1}{y}$, implying
> 
> $$
> (x', z') = \left(\frac{1}{y}, \frac{z}{y}\right)
> $$
> 
> Finally, we can take a third plane where $z = 1$. Since all lines in $\mathbb{R}^3$ must pass through one of these planes, and since we can construct a similar chart, then we need only three charts to cover $\mathbb{RP}^2$. 

## The Induced Topology
Since charts map subsets of a manifold $M$ to subsets of $\mathbb{R}^n$, which has a natural topology, we may use these charts to transfer this topology onto $M$. Such a topology is called the *induced topology*.

We define the induced topology by defining which sets are considered open:

Given a set $M$ with an atlas, a subset $V \subset M$ is *open* if, for every chart $(U, \varphi)$ in the atlas, $\varphi(U \cap V) \subset \mathbb{R}^n$ is open. The collection of these open sets form a topology on $M$.

Importantly, to check that a set is open, it's clearly unnecessary to check sets $U$ that contain no points of $V$, as the null set is always open in a topology. 

# Smooth Structures

As mentioned earlier, we intend for manifolds to represent the idea of "smooth looking" spaces, and in particular, we will eventually be able to define such as integration or differentiation over them. Toward this end, we want to formalise this notion of a smooth structure, which will give a clear and well defined concept of a smooth function.
## Equivalent and Maximal Atlases 
If I define one atlas on a manifold, and you define another, we are still discussing the same manifold. For example, on the plane, I may choose to use Cartesian coordinates, while you choose to use polar. The choice of atlas is not essential. 

Two atlases are *equivalent* if their union is an atlas.

> [!Exercise] Exercise
> To make this definition clearer, consider the topology induced by two equivalent atlases.
> 
> Let $\mathcal{A} = \{ (U_\alpha, \varphi_\alpha) \}$, $\mathcal{B} = \{ (V_\beta, \psi_\beta) \}$ be two equivalent atlases on $M$.
> 
> Then $\mathcal{A} \cup \mathcal{B}$ is also an atlas, so $\{ (U_\alpha, \varphi_\alpha) \}$ and $\{ (V_\beta, \psi_\beta) \}$ are compatible. That is,
> 
> $$
> \psi_\beta \circ \varphi_\alpha^{-1} \colon \varphi_\alpha(U_\alpha \cap V_\beta) \longmapsto \psi_\beta(U_\alpha \cap V_\beta)
> $$
> 
> is a diffeomorphism, with $\varphi_\alpha(U_\alpha \cap V_\beta)$, and $\psi_\beta(U_\alpha \cap V_\beta)$ open.
> 
> Let $\tau_\mathcal{A}$ be the topology induced by $\mathcal{A}$. If $A \in \tau_\mathcal{A}$. Then $\varphi_\alpha(A \cap U_\alpha)$ open, and so
> 
> $$
> \varphi_\alpha(A \cap U_\alpha) \cap \varphi_\alpha(U_\alpha \cap V_\beta) = \varphi_\alpha(A \cap U_\alpha \cap V_\beta)
> $$
> 
> is open. It follows that
> 
> $$
> \psi_\beta(A \cap U_\alpha \cap V_\beta) = (\psi_\beta \circ \varphi_\alpha^{-1})\left(\varphi_\alpha(A \cap U_\alpha \cap V_\beta)\right)
> $$
> 
> is open, since $\psi_\beta \circ \varphi_\alpha^{-1}$ is a diffeomorphism. Since $\psi_\beta$ is bijective with domain $V_\beta$, we have that
> 
> $$
> \psi_\beta(A \cap V_\beta) = \psi_\beta \left(\bigcup_{\alpha} (A \cap U_\alpha \cap V_\beta) \right) = \bigcup_{\alpha} \psi_\beta (A \cap U_\alpha \cap V_\beta)
> $$
> 
> which is the union of open sets, and hence, also open.
> 
> Then two equivalent atlases induce the same topology. 

An atlas is *maximal* if any chart, compatible with every chart belonging to the atlas, belongs itself to the atlas.

In other words, an atlas is maximal if there is no larger atlas containing it. In particular, an atlas is maximal precisely when any equivalent atlas already lies inside of it.

## Smooth Structure
A *smooth structure* is a maximal atlas.

This definition allows us to re-characterise the definition of a diffeomorphism as follows:

A *diffeomorphism* is a bijection between two sets with smooth structure which, when used to identify the points of the two sets, also identifies the smooth structures.

This makes formal the idea that diffeomorphisms are the maps which preserve smooth structures, i.e. isomorphisms of smooth structure.