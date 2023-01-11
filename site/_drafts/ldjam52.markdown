---
layout: post
title:  "How I created my game for the Ludum Dare 52"
date:   2023-01-10 00:08:30 -0300
excerpt: I pushed myself to make a 3d game for Ludum Dare 52 last weekend and felt like a beginner during the process but it felt really good and I learned new things. I am super happy with that. In this blog post I will share the experience and some of the technical details of the solution. 
---

I pushed myself to make a 3d game for [Ludum Dare 52](https://ldjam.com/events/ludum-dare/52/$319699) last weekend and felt like a beginner during the process but it felt really good and I learned new things. I am super happy with that. In this blog post I will share the experience and some of the technical details of the solution. 

<div class="post-image">
    <img src="/assets/ldjam52-travel_01.gif" />
    <span>The Harvester exploring the sands of Arrakis</span>
</div>

When I first read the jam’s theme, **"Harvest"**, the first thing that came to my mind was Dune 2, the 1992 PC game. I played a lot that game when I was young and I remember it with much love. One of the first things I recall was the feeling of anxiety when the Sand Worm was coming to get your harvester, the most important unit in the game, and you had to forget everything else to save it. 

After thinking a bit about the theme and about Dune, I decided to create a game around that feeling. 

At first I wasn’t sure if I wanted to give the player the power of the Shai-hulud and the game was going to be about hunting harvesters or the other way around, you are the harvester escaping the god. I decided to go for the second just because the game was more clear in my mind.

Basically, in the game you control a Harvester and have to harvest spice, at some point a Sand Worm tries to eat you and you have to escape and repeat until a target spice is reached or until you get eaten.

### The Dunes

The first thing I wanted to achieve was the sensation of Arrakis’ dunes and for that I decided to use Unity’s terrain tool which I never used before and I quickly managed to have something I liked.




### The Harvester

__TODO: share about the controller, and how I did the model rotation given the wheels projected on the terrain__

### The Mini Map

To create a game in 2-3 days, you have to make a lot of shortcuts and hacks, and the Mini Map has a lot of those.

I don't know the correct solution for a Mini Map, never had to do something before, my approach in this case was to create a secondary camera that follows the Harvester far from above and to render it to a render texture, to later show that render texture in the UI.

<div class="post-image">
    <a href="/assets/ldjam52-minimap-camera.png"><img src="/assets/ldjam52-minimap-camera.png" /></a>
</div>

In order to control what to render, I created a GameObject with a colored Quad inside all the elements of the game I wanted to be shown in the map and set them in special layer named Scanner, that is set in the  Culling Mask of the Mini Map camera. I added those objects to the Harvester, the Sand Worm and each of the Spice instances and I normally had to scale them up in order to increase their importance in the map. 

<div class="post-image">
    <a href="/assets/ldjam52-minimap-icons.png"><img src="/assets/ldjam52-minimap-icons.png" /></a>
</div>

And finally, to render that in the screen, I used a UI Raw Image with the render texture set and configured the size I wanted to show it.

<div class="post-image">
    <a href="/assets/ldjam52-minimap-ui.png"><img src="/assets/ldjam52-minimap-ui.png" /></a>
</div>

Even if it wasn't a horrible approach, there are probably better ways to render that Mini Map texture with more control of what to show without having to create tons of GameObjects, but it was a great approach for the jam in my opinion.

### The Sand Worm

The main thing I wanted to achieve with the Sand Worm was the moment it eats your Harvester and the first thing that came to my mind was an image of different rings of teeth rotating at different speed and that was my focus. So basically the worm will travel hidden in the dunes but shown in the Mini Map so you can react, and it will came out to eat you when below the harvester.

<div class="post-image">
    <img src="/assets/ldjam52-sandworm-attack.gif" />
    <span>Sand Worm attack sequence</span>
</div>

To create the model I used ProBuilder tool. For the body I used a Pipe modified to make the mouth a bit bigger and then a set of Cones for the teeth. 

<div class="post-image">
    <a href="/assets/ldjam52-sandworm-01.png"><img src="/assets/ldjam52-sandworm-01.png" /></a>
    <span>The Game Object used for the Sand Worm.</span>
</div>

<div class="post-image">
    <a href="/assets/ldjam52-sandworm-02.png"><img src="/assets/ldjam52-sandworm-02.png" /></a>
    <span>Each element of the Sand Worm.</span>
</div>

For the teeth rotation I just used a script that rotates the euler angles given a speed and the delta time and I configured different speeds for each ring.

Finally, for the eat sequence, I used two different Cinemachine Virtual Camera that are switched back and forward.

### What I used to make the game

Sometimes people ask me which language and/or technologies I used to make a game, and I though not so frequent I share an extensive list, here it is:

* Unity and C#
  - Terrain Tool
* URP (Universal Render Pipeline)
* Post processinig effects like bloom, vignete, etc.
* Probuilder
* Cinemachine

## Conclusion

For a game I created in two days (I started one day late) and maybe in a total of 12 hours, I am quite happy with the results and all the generated knowledge. I had some doubts at first since I knew I wasn't going to spend the first day and I was going to work some sparse time (I am a father of two) but now I know I want to repeat it. 

By the way, if you liked the post and the game, [follow me on twitter](https://twitter.com/arielsan) and [share](https://twitter.com/arielsan/status/1612526561181196297
), and also play the game at my [ichio page](https://arielsan.itch.io/spice-must-flow) or in the [Ludum Dare's Entry](https://ldjam.com/events/ludum-dare/52/$319699).

