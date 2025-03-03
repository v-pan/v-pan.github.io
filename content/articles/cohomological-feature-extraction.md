---
title: Cohomological Feature Extraction
draft: false
date: 2024-03-20
---

<style>
@media(min-width: 800px) {
	.row p {
		display: flex;
		align-items: center;
		gap: 2rem;
		img {
			min-width: 0;
		}
	}
	.half {
		width: 100%;
		img {
			width: 25vw;
		}
	}
}
</style>

A topological data analysis method

# The Neural Decoding Problem
A pressing question in neuroscience is to figure out the relationship that between some stimulus and the neural response.

For example:
- Head direction cells fire when an animal faces in a particular direction.
- Place cells fire when an animal is in a particular location, known as a place field.

Determining this relationship is, unsurprisingly, quite hard. If allowed to freely roam, then an animal will be exposed to all sorts of sounds, smells, and sights. On the other hand, excessively restricting them will also introduce new, undesired stimuli and responses. All of this and more, however, are contributing factors that could drive neural activity. How then, do we decide which matter?

In an ideal world, we wouldn't even have to. If we collect enough data, one can dream that the data could simply suggest the relevant features itself; or in other words, that we could use the data to select a model for us. This is typically done with methods from the field of data analysis, however, as we'll explore later, these methods have an important limitation.

# Spike Trains

A common way to represent neural activity is with a spike train. Put simply, a spike train is a way we turn a continuous voltage reading into a series of discrete events, by picking some threshold voltage above which the neuron is considered "firing". 

As mentioned previously, place cells fire, in this thresholded sense, when an animal finds itself in a particular location. Below is an idealised picture of how a trajectory through a space might be broken up between place cells, and beside it, the corresponding spike train we would observe. Each colour corresponds to an imagined place cell.

<div class="row">

![](https://lh7-rt.googleusercontent.com/slidesz/AGV_vUf8fQJg4bAy-YN7W7TPmu66ghQFaYGPfUsSi-CjXFNPvSnTyJfrE8__V2diwH8Z-0QXk2IU4KnXpiBx5JdimkJ3yki_kNt8vRQ1SgZdJLqKHBHNnjPFTpfTrqdoYi5O7rIvabxedUFpCZeERccHHI8Teg=s2048?key=Um2t5XrIDYkjjwBY5w6Yqg)
![](https://lh7-rt.googleusercontent.com/slidesz/AGV_vUfArrloxuOXIjwqLie1AfKcrWyCgjHSBHnUgfWYK5Mrbr06UEyIownWglnDwLOIppaBM6FdUhed7aZALlLg6Ekw41g8uJTcXajZ2LFUt1CEwisHgrbqSaRAqRJ3OKYcj1R1b9zRHqARZOutgRuwTrhy=s2048?key=Um2t5XrIDYkjjwBY5w6Yqg)

</div>

Notice that there are overlaps between different neurons firing. These correspond with times when our trajectory is passing from one place field to another, as these regions have a slight overlap in the physical space the cells are tuned to.

Remember that our goal was to find ways of extracting important features from the data. Looking at this correspondence backwards, if we started with only an observation of the spike train, it seems reasonable that we could attempt to reconstruct this place cell map - even if we've never seen it!

The spike trains can't give us more information than overlap; for example, we would have no way to recover how large these place fields are. Some are large, some are small, but the spikes will look the same. This means that we can't recover distances, but we can recover *structure*, and this hints that we're going to need to look at the problem topologically.

# Representing Topology

Typically, we would record the activity of each neuron, and assign them all their own coordinate. Together, $n$ neurons would build up an $n$-dimensional *Euclidean* space $\mathbb{R}^n$, but this is a problem if we intend to work topologically. Namely, given that our space is fixed to be Euclidean, how do we actually represent the topology?

First, we consider the simpler problem of representing the topology given the place fields.

## First Attempt: Adjacency Graphs

A naive first attempt would be to use a graph. In particular, we can start with an edgeless graph, and give each place field a vertex. Wherever there's an overlap between place fields, we then draw an edge between them. The end result will look something like this

<div class="row">

![](https://lh7-rt.googleusercontent.com/slidesz/AGV_vUf8fQJg4bAy-YN7W7TPmu66ghQFaYGPfUsSi-CjXFNPvSnTyJfrE8__V2diwH8Z-0QXk2IU4KnXpiBx5JdimkJ3yki_kNt8vRQ1SgZdJLqKHBHNnjPFTpfTrqdoYi5O7rIvabxedUFpCZeERccHHI8Teg=s2048?key=Um2t5XrIDYkjjwBY5w6Yqg)
![](https://lh7-rt.googleusercontent.com/slidesz/AGV_vUdLnhY5Q6V1zUuTK5q_eVWke3af8a8dvIkpIpYV_k2xRvM-SiJ-3936PolkvoOL1boEbyGaExc2ypTVzmiKMof0hBrY7M0v1cYX-yeUu0LmJtY9J_7hC-oV50-dteFwST4advUNEPVmu8lFTJ_gGIHi=s2048?key=Um2t5XrIDYkjjwBY5w6Yqg)

</div>

but there's an issue here. If we look at the place fields we are trying to represent, there's no gap between the blue, green and orange fields. In our graph however, we have two gaps, the larger one corresponding to a real feature of the topology, and the smaller one which arises as an artefact of the graph representation. This isn't ideal.

The key problem is that edges can only represent an adjacency between two neurons. In the case that three overlap simultaneously, this makes it ambiguous what kind of overlap they have. A solution, then, would be to introduce new kinds of edges - ones which can connect three place fields, or even more, simultaneously.

## Second Attempt: Simplicial Complexes

Taking inspiration from our observation, we introduce "higher dimensional" edges to our graph. In this instance, instead of drawing an edge between two nodes, we'd like to draw a face between three, like so:

<div class="row">

![](https://lh7-rt.googleusercontent.com/slidesz/AGV_vUfWzjxZihn38JbGUV_OSiOLwHtAXE3Rr12IVjqh9s7rGwfwBEX3bhX2b80pzXtC2-RUSsHLvLVoPDT2qIyksSC0OqtXphhx-QypmXeyLbd7h9g84zn91O6pIgUT5wQsYNOtZW3ZWBWmu3-loWLKXU1Xsza1F8_4=s2048?key=Um2t5XrIDYkjjwBY5w6Yqg)
![](https://lh7-rt.googleusercontent.com/slidesz/AGV_vUe0zKIsKOJDha-jY4tISy_bk9YTEMgWpDJ79JEMjMyoQAQBqGbKbq6u_mRQ7MCMwDp3zVnMYWxn6N00zzGE_PKvKz4NKYROC-jREzn_P4mmeuef-QI0kvmpg5UMeERFyPC9uLKIQXqKbyZ9PBUg4xU_-13HYfH3=s2048?key=Um2t5XrIDYkjjwBY5w6Yqg)

</div>

This resolves the previous ambiguity, and we can continue in a similar fashion when we have more overlapping vertices. If four fields overlap, then we draw a tetrahedron between them. In general, we call these the **regular simplices**:

- $0$-simplex is a vertex,
- $1$-simplex is a line,
- $2$-simplex is a triangle,
- $3$-simplex is a tetrahedron,

and so on, where an $n$-simplex is an $n$-dimensional convex hull. When $n+1$ fields overlap, we can connect them with an $n$-simplex. The collection of these simplices we've built is then called a **simplicial complex**. This representation allows us to unambiguously determine where there is a hole, and consequently, accurately capture the topology of our fields.

## From Fields to Points

We've found a representation that works, but relied on knowledge of the fields to do so - which we can't observe. If we want to use this representation, we need to come up with a method that can construct the topology that works directly on the data.

There's a two main challenges here:

1. We measure *points*, not fields. Fields naturally told us when they overlap, but distinct points never naturally overlap.
2. We only get a *sample* of when neurons fired. It is difficult, if impossible to know ahead of time what sample rate will appropriately measure the topology of the place fields.
	- For example, suppose we sampled the idealised place cells above.
	- It's possible that, in a particular run, we get no samples where three place cells fired together. This may be because we did not have a high enough resolution in our sample, or because the trajectory never crossed it.
	- On the other hand, our signal is noisy, and we need to average over it to ignore random, unrelated firing. If we oversample, then we might record overlaps where there were none.
	- It's then possible, if not likely, that we will end up missing or misrepresenting the topology.

Both of these problems are, in some sense, problems of scale. Looking at both cases, for:

1. We need to decide on a notion of "closeness" for our points, where two points who are "close enough" are considered to overlap. This distance naturally implies some kind of scale to the data, where anything below the distance is considered small on this scale.
2. Similarly, we need to decide when multiple samples are close enough to be considered the same sample, or when they're spaced apart enough to be considered distinct. This notion of scale aligns with the one in case 1.

Our solution, then, will have to:

1. Construct a simplex on points
2. Account for scale-dependence when doing so

## Final Attempt: The Čech Complex

We first deal with how to construct a simplex on points using the **Čech complex**. 

In our previous terms, the construction takes some characteristic distance or scale $\varepsilon$, and if any points are within this distance from each other, then we consider them to overlap.

Formally

1. The construction starts with points in some $n$-dimensional space.
2. Around every point, we place an $n$-dimensional ball, and give it a radius $\frac{\varepsilon}{2} > 0$.
3. If this overlaps the $\frac{\varepsilon}{2}$-ball around another point, construct the $(k-1)$-simplex connecting the two points.

Note that this construction means the distance between any two points in a simplex is at most the sum of the radii, $\varepsilon$, which is our characteristic scale.

You can see the construction of the Čech complex in action at Nathaniel Saul's [[https://sauln.github.io/blog/nerve-playground/|Čech Complex Playground]].

### Filtrations

Equipped with the Čech complex, we can now construct simplices, solving our first problem. We are still left, however, with the second. How do we decide on the "right" scale?

This problem is, understating it a little, quite difficult. Difficult enough, in fact, that we make an assumption here in order to make some progress. In particular, we assume that features that persist for a wide range of scales are "true" features of the data. Otherwise, features persisting only on a very narrow range of scales are probably noise. While this might seem a little unjustified, it *is* at least useful, so we will continue with it.

It follows then that it would be more helpful to have a summary view over all, or at least as many, values of $\varepsilon$ as possible, and the topologies associated with them. To get this, we construct the **filtration**, which is just a sequence of Čech complexes with increasing values of $\varepsilon$.

# Persistence Diagrams

This seems like a cool reference on persistent homology: https://www.ams.org/journals/bull/2009-46-02/S0273-0979-09-01249-X/S0273-0979-09-01249-X.pdf