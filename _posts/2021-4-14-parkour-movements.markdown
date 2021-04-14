---
layout: post
title:  "Parkour Movements"
categories: update
---
This week I accomplished 2 kinds of parkour movements: wall running and platform climbing. 
There is one in-progress, which is free climbing (I will complete it soon).
I am ready for building a more completed level.

# Parkour Movement Basis
Supporting parkour movements in motion matching context is not easy.
To make the problem tractable, I choose to stick to one parkour trajectory once it has been chosen.
In this way, I only need to find the "entry point" and `Kinematica` will handle the transition.
But how can I know which trajectory I'm going to transition into?

`Kinematica`'s official demo comes with a solution.
For each parkour animation clip, there are some annotations set manually on it.
- The `Anchor` annotation determines how to place the trajectory in the world given the contact transform of the player with an obstacle.
  It is a delta transform of the root on the trajectory at the time when the first contact happens related to the contact transform.
- The `Contact` annotation determines whether the trajectory is valid throughout its time.
  These annotations mark all transform of the contacts with the environments along the trajectory.
  At run time, the transition job will examine that all contacts are on the surface of some colliders.
- The `Escape` annotation marks the end of the trajectory.

Once a contact is detected along the trajectory, and if the contact object supports such movement (with certain tag), and also if the player is holding the special movement button, a transition is issued.
Kinematica then searches for the closest pose between the original trajectory and the part of the target trajectory before the first contact, and does the transition at that point.

I adapted my code based on that, and added some my own constraints.
I limited the angle of the anchor of source candidates to the player's input direction to gain more accurate control.

After struggling and figuring out how the whole thing works, I got the following results.

<div style="text-align: center;"><iframe width="560" height="315" src="https://www.youtube.com/embed/5fB2VVVg7QM" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></div>

<div style="text-align: center;"><iframe width="560" height="315" src="https://www.youtube.com/embed/KJk7StOtTZU" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></div>

# Animation Correction
Having wall running working is a success, but I want more than that.
I want her to be able to run on a vertical wall, since it will help me a lot in level design.
So I set up a quick scene which temporary rotates the wall (and the character) and rotates back, and re-record the wall running animations.

<div style="text-align: center;"><iframe width="560" height="315" src="https://www.youtube.com/embed/HfAP-VA6AY8" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></div>

# Platform Climbing
The next movement is to let the character climb onto platforms of a certain height.
As I have already got the anchored transition work, I could just reuse the same piece of code.

<div style="text-align: center;"><iframe width="560" height="315" src="https://www.youtube.com/embed/dYtRqqwCmZo" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></div>

However for climbing, it will look better if both of her hands could snap on the ledge of the platform.
To archive this, I calculate the closest point of her hand to the ledges of the platform she's going to climb onto, and snap her hand onto it when the timing is right.

# Free Climbing
By the time this post is written, I am still working on free climbing.
That's will be the showcase composed of only "IK animation" (for locomotion that's "motion matching", and for platforming that's "motion matching combined with IK animation").
It won't be too hard compared to the previous one, since there have already been implementations online.

After finishing free climbing, all core mechanisms of the showcase are done.
I will spend the remaining time building a longer level for the showcase.

# Builds
- [Windows Build](https://drive.google.com/file/d/1zx-eZl2GfTZeYuOy4NTngPfaBk-aTbS_/view?usp=sharing)
- [Linux Build](https://drive.google.com/file/d/1dWk0FtpIyt72BIKF64Lz5CmeSVrc1_EC/view?usp=sharing)