---
layout: post
title:  "Meet Chen!"
categories: update
---
# Fix Root Motion
Last time I left the kinematica build an issue: the root movement doesn't confine with the steps.
So I examine the contents of the animations I recorded, and it looks like this.

<img src="{{site.baseurl}}/assets/2021-3-31-old-animation-clip.png">

As we can see, it does contain root movement, however, when being used in Kinematica, the results differ by choosing different "root nodes" in the avatar (of the Unity's [Mecanim Animation System](https://docs.unity3d.com/Manual/AnimationOverview.html)).
If the avatar's root node is set to the actual root (which is the parent of all other nodes), Kinematica successfully extracts its root motion, but the body (which is a child of the root) animates in-place, i.e., doesn't follow the trajectory.
On the other hand, if the avatar's root node is set to a node which is not a part of the hierarchy, Kinematica will extract the root motion as well as the body motion, but is not able to do motion matching in runtime.

So I downloaded a few Kinematica examples and study the animation clips used.
I found that there are `Animator.Root` fields in those clips, however, I also found that there was no way to create those fields without importing clips from external assets like FBX scenes.

Luckily, there is an [FBX recorder](https://docs.unity3d.com/Manual/com.unity.recorder.html) package provided by Unity, so I now use it to record the animation into an FBX file, set its avatar inside the importer, and then extract the animation clip from it.

# Better Kinematica
In practice, I find that Kinematica works the best with long, continuous or a coherent set of animation clips.
So I integrate several preset paths into one and that one performs the best.

Also to increase controllability, I record several start-stop clips and enlarge the threshold of idle pose matching.

# So Who On Earth Is Chen?
Maybe you have the question where the project name "[Chen](https://en.touhouwiki.net/wiki/Chen)" comes from, and here she is!
<img src="{{site.baseurl}}/assets/chen.png">

Until now I only use a few boxes and spheres to represent my model.
I would like to see how it performs on a real rigged model.
So I created a model for her (Sorry no faces):

<img src="{{site.baseurl}}/assets/2021-3-31-chen.png">

Here I am using animation rigging package to bind the model to the target.
After taking some time to tweak, I think I have got a pretty decent result.

Next step I will configure the full-body animations as well as try to make her leave the flat ground.
Enjoy!

# Builds
- [Windows Build](https://drive.google.com/file/d/1zx-eZl2GfTZeYuOy4NTngPfaBk-aTbS_/view?usp=sharing)
- [Linux Build](https://drive.google.com/file/d/1dWk0FtpIyt72BIKF64Lz5CmeSVrc1_EC/view?usp=sharing)