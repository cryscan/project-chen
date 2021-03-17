---
layout: post
title:  "Custom Paths"
categories: update
---
The main contributions of this week are
- Implement custom path points
- Implement user-defined durations and gaits

I also looked into user-defined terrains.
But due to midterms of other classes in this week, I didn't implement it.

# Path Points as Constraints
This week I re-studied the methods of creating custom path points I mentioned last time:
1. Connecting several short trajectories
2. Customizing trajectory initializations
3. Path constraints

As I have already stated, the 1st option has many drawbacks, in which the most fatal one is that the choice gait sequence of each individual trajectory is limited, and sometimes is even not available.
The reason is that it is required that at the end of each trajectory, all limbs go back to the ground.

The 2nd option was the one I examined at first this week.
But after trying it, I found that the optimization changed the initialization a lot.
The character didn't go through the path points at all.

So I ended up using the 3rd method, which is adding new constrains into the optimization problem.
Fortunately, after studying the source code of the library on constraints, I found it not hard to implement one of my own.
The constraint takes a vector of times, linear positions and angular positions, and limits the body's movement at the given time points.

# Custom Gaits
As the users are able to define their own paths, the lengths and durations may vary.
If the duration and the gait sequence are still fixed, there may be no solutions.
So I extended to interface so that both can be customized.

Also, users are now free to switch whether to optimize gaits or not.
Gaits optimization are useful when travelling on complex terrains, but may lead to unwanted gaits if being on flat ground.

The whole component used in Unity is shown in the screenshot below.
Please note the list of path points and gaits.

<img src="{{site.baseurl}}/assets/2021-3-17-path-points.png">

# Terrain
The library accepts height maps as terrain.
One need to fill in height and gradient at the specified point to define a height map.
I decide to do ray casts on grid points in Unity to gather the data, and pass the data to the library and calculate the gradients there.
I am seeking for a way to transfer such huge amount of data from C# to the library.

# Builds
I spent several hours today and finished compiling the C++ codebase (with dependencies `ipopt`, `ifopt` and `towr`) on Windows,
and successfully imported them to Unity.
However, the `ipopt` solver doesn't work because the dependency of a linear solver (`HSL`) doesn't meet.
This solver requires a licence.
I have already sent an application and am waiting for response.
So there is still no Windows build this week, sorry...

- [Linux Build](https://drive.google.com/file/d/1dWk0FtpIyt72BIKF64Lz5CmeSVrc1_EC/view?usp=sharing)

<div style="text-align: center;"><iframe width="560" height="315" src="https://www.youtube.com/embed/0e9B8slxvFI" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></div>