---
layout: post
title:  "Terrain, Kinematica and Dynamatica"
categories: update
---
This is a productive week, in which I implemented custom terrain, re-organized all codes into a package called `Dynamatica`, and tried motion matching with `Kinematica`.

# Custom Terrain
As planned, it is the first priority to get the custom terrains work.
Terrains are based on height-maps, which have been introduced in many game development tutorials.
Here our height-map is represented by a matrix of heights at corresponding locations of vertices.
Neighboring vertices are connected by triangles.

In order to get the height of arbitrary location, I need to first locate in which triangle it is, and then calculate the height of that point.
The method used here is called [Barycentric Interpolation](https://en.wikipedia.org/wiki/Barycentric_coordinate_system).
I would like to interpret it as transforming to a coordinate frame whose bases are the two sides of the triangle.

In the library, terrains are stored as separated objects from sessions, and concurrency has been taken care of.
So different sessions can read the same terrain at the same time.

In Unity, the terrain is built by an object called *Terrain Builder*.
On awake, it cases a matrix of rays from the top and transmits the height data to the library.
The user only needs to determine its origin, range and number of segments.

<div style="text-align: center;"><iframe width="560" height="315" src="https://www.youtube.com/embed/pBwIYQKpE5k" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></div>

<div style="text-align: center;"><iframe width="560" height="315" src="https://www.youtube.com/embed/jjlUERmPJEw" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></div>

# Animation Recording
In order to create animation clips from that, I discovered *Game Object Recorder* in Unity, and made a recorder which activates when replaying.
The following video shows one recorded clip.

Note: The root position in the video is wrong.
I have changed the hierarchy structure of the model later so the root position is correct now.

<div style="text-align: center;"><iframe width="560" height="315" src="https://www.youtube.com/embed/IQCPPnjIuLE" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></div>

# Introduce Dynamatica
At last week's meeting, I was advised to change the name of the component from "Hopper".
So I did a little further: I re-organized all codes related and put them into a namespace called `Dynamatica`.
As you may guess, this name is derived from Unity's `Kinematica` package, which implements motion matching.
Since the tool I'm working on relates more on the dynamics part, I give it this name.

<img src="{{site.baseurl}}/assets/2021-3-24-dynamatica.png">

It contains the common user interface for optimizing (`Dynamatica`), terrain (`Terrain Builder`, `Terrain Tracker`) as well as utilities to customize paths (`Path`, `Path Node`).

# Dance Card and Motion Matching
After all these above have done, I began to record animations for a biped model to create a database.
I found a [post](https://zhuanlan.zhihu.com/p/136971426) about how real studios do motion capturing, in which they introduced the dance card they used in recording.

Mimicking that, I created several paths on the flat ground.

<img src="{{site.baseurl}}/assets/2021-3-24-path.png">

After recording several clips, I imported them into `Kinematica` and created a real-time demo following the examples.
However for now the result doesn't look good since there are too few clips.
I will record more and improve the motion matching quality in the next week.

# Builds
This week I finally get the windows build working (without using `HSL`).
Please try it.
I include 2 optimization scenes and 1 real-time scene.
Press number 1, 2 and 3 to switch scenes.

- [Windows Build](https://drive.google.com/file/d/1zx-eZl2GfTZeYuOy4NTngPfaBk-aTbS_/view?usp=sharing)
- [Linux Build](https://drive.google.com/file/d/1dWk0FtpIyt72BIKF64Lz5CmeSVrc1_EC/view?usp=sharing)