---
layout: post
title:  "Road Map"
categories: update
---
Before conducting any experiments I want to make it clear how the current high-level plan of the whole project is.
As mentioned in my [previous post](2021-1-30-academic-researches.markdown), the goal is to make a procedural animation pipeline that
- Generate physically correct animations
- Can be used in real-time applications
- Are affordable to indie game studios

So not to be in detail, I plan to do it in the following steps:
1. Get Contact Invariant Optimization work in python simulator
2. Design different tasks for the agent to learn in
3. Design sensors as the inputs to the agent
4. Try to either learn a kinematics policy (which generates kinematics animation directly from inputs) or a dynamics policy (which outputs actions)
5. Import the policy into the game engine
6. Make a lovely non-humanoid character and make it a parkour game

If the above plan fails somehow, I also have a fallback option: try using trajectory optimization clips and motion matching.