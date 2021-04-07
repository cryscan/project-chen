---
layout: post
title:  "Full Animation and Contact Detection"
categories: update
---
# Full Body Animation
One of the limits of the proposed animation generation algorithm is that it can't simulate hand and torso movements, since limbs are assumed massless.
I am not saying that running with both arms in T-pose looks bad, but that's not a complete running animation.
To compensate it, I have to make arms swing using the information I got.

During first days of this week I somehow came across with the following video demonstrating the author's work on procedurally animating a humanoid character.

<div style="text-align: center;"><iframe width="560" height="315" src="https://www.youtube.com/embed/0LvcrLpWUjs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></div>

In that video, the author connects a hand's IK target to the foot on its opposite site.
This is a brilliant idea from which I can borrow.
Actually I ended up going further than that.

<img src="{{site.baseurl}}/assets/2021-4-7-arms.png">

As shown in the diagram I draw above, I set up an anchor transform for each arm, whose origin is the position of the hand in nominal pose, and its forward axis is the direction the arm swings.
Also, I am using a parabola to represent the hand trajectory instead of using a straight line to gain better result.

Only swinging arms in the indicated directions is somehow lifeless as a robot, and I later figured out that adding more reactive movement to the arms and hands adds a lot.
The additional movement I add is reaction to centrifugal forces.
Its implementation is rather easy, by calculating the angular velocity of root and displace the hands along the outwards direction.

Finally, there is no harm to add a little more torso tilting, which is proportional to the difference of displacements of both hands.

The result is shown in the video below.

<div style="text-align: center;"><iframe width="560" height="315" src="https://www.youtube.com/embed/itWcQLm3kFY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></div>

# Contact Detection
So far Chen clips through anything she meets with, because there is no contact detection.
Traditionally, this is usually handled by the character controller, and the animator plays the corresponding clips based on velocity.
However here the root motion is controlled by the animation clips themselves, so a different approach is needed.

Luckily Kinematica has a [demo](https://github.com/Unity-Technologies/Kinematica_Demo/) showing how to detect collision in this framework.
It takes advantage of trajectory prediction shipped with the package.
In each frame, it moves a character collider along the trajectory.
If collision happens, the collider deviates from the original trajectory so that the trajectory should be updated.
In this manner, the motion matching algorithm can know ahead that a collision is going to happen and find a stop transition smoothly.

As shown in the video below, she stops at the wall when I pushing against it.
If the collision angle is small, she can even circumvent the obstacle.

<div style="text-align: center;"><iframe width="560" height="315" src="https://www.youtube.com/embed/QeHNb-9Lixk" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></div>

# Parkour
I have also started with parkour movement (wall running, jump, etc), but this part is still buggy so I decide not to show in this post.

# Builds
- [Windows Build](https://drive.google.com/file/d/1zx-eZl2GfTZeYuOy4NTngPfaBk-aTbS_/view?usp=sharing)
- [Linux Build](https://drive.google.com/file/d/1dWk0FtpIyt72BIKF64Lz5CmeSVrc1_EC/view?usp=sharing)