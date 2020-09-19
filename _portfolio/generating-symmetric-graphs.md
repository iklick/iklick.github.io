---
title: "Generating Graphs with Symmetries"
excerpt: "A brief overview of my work in generating graphs with desired symmetries.<br/><img src='/images/500x300.png'>"
collection: portfolio
---

## Graph Symmetries

Graph symmetries have been shown to play an important role in _controllability_ and _synchronization_ among other topics.
While it has been long understood how to find symmetries in a graph, the reverse question, how to create a graph with arbitrary desired symmetries, has not been as well understood.
In my papers

1. [Generating Symmetric Graphs](/publication/2018-12-11-generating-symmetric-graphs)
2. [Generating Graphs with Symmetry](/publication/2018-10-11-generating-graphs-with-symmetry)

which culminated in my talk at SIAM Conference on Applications of Dynamical Systems as a part of the session Effects of Symmetries and Partitions on Dynamics in Networks (Part I).

This page gathers together my thoughts on this topic and attempts to present the main result in as general a framework as possible.

### Graphs

Before moving on though, let me be concrete by what I mean by a graph.
I will present the results for _undirected_ graphs but extending the results to _directed_ graphs is not too difficult.

A graph \$ \mathcal{G} = (\mathcal{V}, \mathcal{E}) \$ consists of \$ \lvert \mathcal{V} \rvert = n \$ nodes and \$ \lvert \mathcal{E} \rvert = m \$ edges.

> **Definition: Neighbors**
>
> The neighbors of a node, denoted \$ \mathcal{N}\_i \subseteq \mathcal{V} \$, for each node \$ v_i \in \mathcal{V} \$, is the set of nodes which are connected to \$ v_i \$ through an edge, that is,
>
> $$
> \mathcal{N}_i = \left\{ v_j \in \mathcal{V} \vert (v_i,v_j) \in \mathcal{E} \right\}
> $$

> **Definition: Node Degree**
>
> The degree, \$ d_i \in \mathbb{N} \$, of a node \$ v_i \in \mathcal{V} \$ is the number of neighbors of a node, \$ d_i = \lvert \mathcal{N}\_i \rvert \$.

> **Definition: Partitions**
>
> Let \$ \mathcal{S} \$ be a set. A partition of this set, \$ \mathcal{P} \$, is a set of \$ p \$ subsets, \$ \mathcal{S}\_j\$, \$ j = 1,\ldots,p \$ that are _covering_ and _disjoint_.
> The _covering_ property ensures that every element \$ s \in \mathcal{S} \$ implies \$ \exists j \$ s.t. \$ s \in \mathcal{S}\_j \$.
> The _disjoint_ property ensures that every element \$ s \in \mathcal{S} \$ appears in only one subset.

A partition of the nodes of a graph is also called a _coloring_.
Each subset of nodes in the partition is called a _cluster_.

> **Definiton: Cluster Degree**
>
> The cluster degree of a node, \$ \boldsymbol{c}_i \in \mathbb{N}^p\$, 

> **Definition: Permutations**
>
> A permutation is a _bijective_ function from a set to itself, often denoted \$ \pi : \mathcal{S} \mapsto \mathcal{S} \$.

Generically, a permutation can be applied to the nodes of a graph, which can also be thought of as _shuffling_ the nodes around (no node is removed or added).
After permuting the nodes of a graph, generically, the set of edges is altered, denoted by \$ \mathcal{G}^{\pi} = (\mathcal{V}, \mathcal{E}^{\pi}) \$.
Without going into details, the graphs \$ \mathcal{G} \$ and \$ \mathcal{G}^{\pi} \$ are _isomorphic_. The _graph isomorphism_ problem is a famously difficult problem in which, surprisingly, many properties are still unknown.

> **Definition: Symmetry**
>
> A symmetry of a graph is a permutation that does not alter the set of edges, that is, if \$ \pi \$ is a symmetry of \$ \mathcal{G} \$, then \$ \mathcal{E}^{\pi} = \mathcal{E} \$.

For simple graphs that represent well understood geometric shapes, symmetries are visually obvious, but for more complicated structures, determining the symmetries of a graph requires computational tools such as [nauty](http://users.cecs.anu.edu.au/~bdm/nauty/).

### Automorphism Group

Symmetries are special permutations that, when applied to a graph, do not alter the structure of the graph.
Because of this, one can apply multiple symmetric permutations to a graph sequentially and still not alter the structure of the graph.
In fact, the composition of two symmetries is also a symmetry.
Also, the inverse of a symmetry is also a symmetry.
These properties can be collected together to show that the set of all symmetries (along with function composition) form a group, namely, the _automorphism group_.

> **Definition: Automorphism Group**
>
> The set of all symmetries of a graph, along with function composition, form the automorphism group of a graph, denoted \$ \text{Aut}(\mathcal{G}) \$.

> **Definition: Orbits of a Graph**
>
> The automorphism group induces a partition of the nodes, \$ \mathcal{O} \$, such that two nodes are in the same orbit if,
>
> $$
> v_j,v_k \in \mathcal{O}_l \text{ if } \exists \pi \in \text{Aut}(\mathcal{G}) \text{ s.t. } \pi(v_j) = v_k
> $$
>
> In words, two nodes are in the same orbit if there is at least one symmetry that maps one of those nodes to the other.

Each node 

## Outline of Method

### Inter-Cluster Connections

### Intra-Cluster Connections
