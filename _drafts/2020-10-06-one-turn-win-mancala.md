---
title: 'Person Interest Problem: First Turn Win Mancala'
date: 2020-10-06
permalink: /posts/2020/10/one-turn-win-mancala/
tags:
  - games
  - tree
  - graph theory
---

# Ruining the Game of Mancala

Mancala is a simple game that consists of 12 bowls, 2 goals, and 48 marbles.
The initial state of the game spreads the 48 marbles evenly over the 12 bowls, 4 marbles per bowl.

Let us label the bowls \$ 0,1,2,3,4,5,7,8,9,10,11,12 \$ with the player's goal labeled \$ 6 \$ and the the opponent's goal labeled \$ 13 \$.

To start each turn, the player picks a non-empty bowl, labeled \$ k \$, with \$ \ell \$ marbles in it, and spreads them evenly into bowls (or goal) \$ k+1,k+2,\ldots,k+\ell \$ where the arithmetic is done modulo 13 (we skip the opponents goal).

If \$ (k+\ell) \mod 13 \$ is _empty_ and not the player's goal, then the player's turn ends.
If \$ (k+\ell) \mod 13 \$ is not empty and not the player's goal, say \$ k' \$, with \$ \ell' \$ marbles (including the one it receives from the first move), then the player takes those marbles and spreads them evenly into bowls \$ k'+1, k'+2, \ldots, k'+\ell' \$ all modulo 13.
This continues until either the last marble lands in an empty bowl or it lands in the player's goal, \$ 6 \$.
If the last marble lands in the goal, then the player can choose a new bowl freely to continue their turn.

## Rules of Mancala

Some definitions are required to move forward.

> DEFINITION: A move is the series of non-empty bowls (not goal) that each previous bowl reaches until the last marble is placed in an empty bowl.

> DEFINITION: A turn is a series of moves such that each move places the last marble into the goal. The turn ends when a move ends in an empty bowl.

Playing against my talented girlfriend, I noticed she seemed able to pick moves that would prolong her turn a distressingly long amount of time.
After losing badly twice, I wondered if there was an optimal series of moves that would guarantee a win?

Each move starts with a selection of a non-empty bowl, and if that move ends with placing the last marble in your goal, then your turn continues to a second move, and if the last marble again ends in your goal, then the turn continues to a third move, until you place the last marble in an empty bowl.
But what if there was a series of moves such that on your very first turn you get all of the marbles into your goal?

If such a sequence of moves exists, how can we find it?

## Tree Search

Let us start by defining a special type of graph called a tree, \$ \mathcal{T} = (\mathcal{V},\mathcal{E}) \$.

1. A tree has no cycles
2. One can define a _root node_, labeled \$ v_0 \$.
3. The nodes can be partitioned into layers, or shells, defined as the sets of nodes of equal distance to the root node.

To efficiently move around the tree, each node must store some information about its neighbors.
Each node (except for the root node), \$ v_j \$, \$ j > 0 \$, in layer \$ \ell \geq 1 \$, has a set of neighbors \$ \mathcal{N}_j \$ such that \$ |\mathcal{N}_j| \geq 1 \$ that consists of a _parent node_, \$ v_i \in \mathcal{N}_j \$ such that \$ v_i \$ is in layer \$ \ell - 1\$ and \$ |\mathcal{N}_j| - 1 \$ _children nodes_ such that each child node is in layer \$ \ell + 1\$.

Trees are a useful structure when solving problems whose solutions are a sequence.
Define the root node as the initial choice for the sequence, this is also the only node in the first layer.
Then, each possible choice for the second element in the sequence is tried and those that are valid are created 
