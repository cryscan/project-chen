---
layout: post
title:  "Simulator and Inverse Dynamics"
categories: update
---
## Framework and Simulator
To be able to manage the project code properly, it's best to have all the stuff fit in a framework.
By searching through the internet, I find two [matlab](https://github.com/robbierolin/Contact-Invariant-Optimization-Project) [repositories](https://github.com/rshum19/CIO) related to Contact Invariant Optimization.
I tried both, only the first one works for me.

This codebase is very clean and direct, which make it easy to get started with.
By running the code, I get the following trajectory.

<img src="{{site.baseurl}}/assets/2021-2-10-cio.png">

However, I decide not to base my code onto this project, because I notice that the whole codebase takes an ad-hoc architecture.
For example, the dynamics model used is fully hand-crafted and fixed, and what even worse is that, this model is hard coded into the cost function!
This means that in future if I'd switch to a new model, I will have to rewrite the whole thing!

But this doesn't mean that it's useless.
Instead, since it's specifically written for re-implementing the paper, I can find every formula with its corresponding code and also some implementation details not mentioned in the paper.
That helps me understand the algorithm better.

The ideal framework needs to have a physics simulator and a bunch of available models, and it would better to provide functions to calculate some essential properties of the model (such as inertia matrix).
Also it's better to align with some degree of "standard" (like supporting common robot description file format, being compatible with off-the-shelf optimization packages, etc.)

Finally I choose [pyrobolearn](https://robotlearn.github.io/pyrobolearn/) as the framework.
Its advantages include:
- Supporting [bullet](https://pybullet.org/wordpress/) simulator
- Dozens of robot models
- Interfaces which are unified and easy to use
- Plenty of implemented control algorithms
- Having a place left for CIO (although not implemented yet)
- Being well documented

Here is a screenshot I took from the simulator.

<img src="{{site.baseurl}}/assets/2021-2-10-bullet.png">

Since the framework hasn't implemented CIO yet, I have to do it by myself.

## Inverse Dynamics
The most complex formulas in the paper are to recover the contact forces and actuated controls using inverse dynamics.
$$ E = m c^2 $$