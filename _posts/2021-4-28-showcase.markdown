---
layout: post
title:  "The Game and The End"
categories: update
---
In the final week I put all my efforts in making a playable demo for the showcase.
At the end of that week, I submitted my work to the EECS 494 + EMU Showcase.

# Climbing
I spent the first two days of the week implementing the free climbing movement.
It is done in totally different way than other movements: by pure procedural animation generated at runtime.
I also added an anchored transition of vertical wall running to transit from normal running to wall climbing.

# First Level
The first level was climbing a mountain.
I designed it as a training of parkour movements with an appropriate learning curve.
By climbing higher and higher, it got more and more dangerous, but the landscape got more and more magnificat.

<img src="{{site.baseurl}}/assets/2021-4-28-mount-front.png">
<img src="{{site.baseurl}}/assets/2021-4-28-mount-back.png">

# Second Level
The second level aimed to test how well the player mastered the parkour skills.
I created several rolling spike traps to test the timing.
I also set up a chamber with movable platforms and switches to form a puzzle testing whether the player could find the way out by the available movements they had.

<img src="{{site.baseurl}}/assets/2021-4-28-octave.png">

# Play-testing
2 days before the showcase I invited my instructor Austin Yarger to play-test my showcase game.
He told me that the movements are fun and easy to perform, but there were still something to improve in that build:
1. Checkpoints. It was too easy to fall off the platforms, and no one wanted to start over every time.
2. Lighting. There was some issue in the lighting. Players couldn't tell whether a wall is climbable.
3. Control. In that build, Chen was like a drunk cat. The control is too slippery.

I also asked my friend Yujia He to play-test my game.

# Polishing
After play-testing, I started polishing the game.
I fixed lighting first by using a toon shader with outlines, which helped the player distinguish the edge of the platforms.

Checkpoint is not as easy as it was thought to be, since the controller was resolving collisions when moved around.
I had to find a work around, which was 
1. Turning off collision detection.
2. Moving the controller to the target position.
3. Setting the transform's position to the target position.
4. Turning on collision detection.

I finally improved the control by movement correction.
Earlier the her movement was purely driven by animation, which caused nondeterministic trajectory.
When her velocity reached a certain level, I reset the root position back to the designated trajectory every frame.
By doing that, the trajectory met the player's expectation.

# Showcase
The showcase was a wonderful night.
A fair bit of people came to play my game and left some valuable comments.
Some people were coming because of the Touhou project, but they all left deep impressions on the fluent animation and the wall-running mechanism.
Here are some of the comments:
- "Love the animation in this game, the movements look really natural even in edge cases, as a person that's big on animations i'm definitely impressed!"
- "I agree with the previous comment, I wish the camera was controllable but otherwise the game-play was solid. I did encounter some of the bugs that was mentioned in the slides, but this game made me super interested in procedural animation. I want to take a look into it now :)"
- "Man the character and her animation is so cute especially the running animation lol. I love it. I know this is a research game but if the background and environment, and the camera movement use some more work, it would be an absolute fantastic one."

I'd say that this showcase game was a success, and there were people who got interested in procedural animation because of it.

# Final Reflection
Looking back at the road-map I drew months ago, I am proud to see that I have done most of the items.
I think I should be thankful that I have the long-term plan for the whole project.
I know what the scope this project is, what technologies I can use, what the final product should it be, and always follow a steady pace.

To sum up, in this project, I
1. Didn't get Contact Invariant Optimization work, but the fallback option worked.
2. Designed different scenarios for the agent to travel through.
3. Managed to use the collected motion data in real time.
4. Made a lovely non-humanoid (?) character and made it a parkour game.

Finally, thank you for everyone who company me along the way.