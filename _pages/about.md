---
permalink: /
title: "Isaac Klickstein's Projects and Musings"
excerpt: "About me"
author_profile: true
redirect_from: 
  - /about/
  - /about.html
---

Hi! My name is Isaac Klickstein, a recent doctoral graduate in mechanical engineering from the University of New Mexico.

This site collects my research projects from my time as a graduate student and my current projects of personal interest. 

I am always looking for collaborators if anything you see here excites you!

## Topics

The following topics make up the vast majority of my time as a graduate school.

### Optimal Control

The field of optimal control concerns itself with finding control signals for dynamical systems to minimize a cost while satisfying some constraints on the states and controls of the system.
An optimal control problem, in general, cannot be solved analytically and numerical tools must be used to _solve_ them (what is meant by solve is discussed in detail in the associated posts).

My interest lies in using numerical solutions to provide insight into analytic solutions.
The analytic solution can then suggest modifications to the numerical formulation to reduce the amount of time to solve.

With the above in mind, I am currently developing a cross-platform, open-source, extremely efficient, and open-source optimal control solver for both practical problem solving and, potentially, as a teaching resource in graduate level optimal control classes.

The library is a work in progress and I am open to any and all suggestions! 

Some background and reference material is contained [here](/optimalcontrol/)

### Equations on Lattices

This project started after, out of curiosity, I solved the minimum energy control problem for an infinitely long chain of single integrators.
Using a discrete Fourier transform, this problem can be solved in detail, unlike general minimum energy control problems.
The scaling behavior found with respect to graph properties offered surprising insight into the control energy for general complex networks including bounds.

There is a lot of potential still I believe in deriving additional results using lattices to better understand control properties in the presence of _near symmetries_, optimal driver node selection, and 

### Graph Symmetries

Understanding graph symmetries is important when investigating controllability, synchronization of multi-agent systems, and consensus.
Finding graph symmetries is fairly well understood (with a few important caveats) while creating graphs with desirable symmetry properties is a new area of research.

