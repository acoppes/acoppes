---
layout: post
title:  "How I created my game for the Ludum Dare 52"
date:   2023-01-12 00:08:00 -0300
excerpt: I pushed myself to make a 3d game for Ludum Dare 52 last weekend and felt it felt really good and I had the opportunity to learn new things in the process. In this blog post I will share the experience and some of the technical details of the solution. 
author: Ariel Coppes
tags:
  - ludumdare
  - unity
  - howto
  - dune
---

Last weekend I joined [Ludum Dare 52](https://ldjam.com/events/ludum-dare/52/spice-must-flow) and decided to push myself out of my comfort zone by making a 3d game with technologies I had no experience with. Even though I felt a bit lost at the beginning, I managed to make a game and had the opportunity to learn some new things at the same time and it makes me really happy. 

In this blog post I will share how I experienced the jam and some of the technical details of the game I made. 

<div align="center">
<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Working on a Dune reference game for <a href="https://twitter.com/hashtag/ludumdare52?src=hash&amp;ref_src=twsrc%5Etfw">#ludumdare52</a> <a href="https://twitter.com/hashtag/ld52?src=hash&amp;ref_src=twsrc%5Etfw">#ld52</a> <a href="https://twitter.com/hashtag/LudumDare?src=hash&amp;ref_src=twsrc%5Etfw">#LudumDare</a> the idea is to harvest Spice while trying to escape from the Shai Hulud, I will not make it in time for the compo but hope to reach the <a href="https://twitter.com/hashtag/ldjam?src=hash&amp;ref_src=twsrc%5Etfw">#ldjam</a> at least. <a href="https://t.co/fRICP6BM8H">pic.twitter.com/fRICP6BM8H</a></p>&mdash; Ariel Coppes Pereyra (@arielsan) <a href="https://twitter.com/arielsan/status/1612127888102555649?ref_src=twsrc%5Etfw">January 8, 2023</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script> 
</div>

When I read the jam’s theme, **"Harvest"**, the first thing that came to my mind was Dune 2, the 1992 PC game. I played a lot of that game when I was young and I remember it with fondness. One of the things I recall is the feeling of anxiety when the Sand Worm was coming to get your harvester and you had to manage to save it while fighting in other fronts. 

I decided to create a game around that feeling. 

Have to admit thought that at first I wasn’t sure if I wanted to give the player the power of the Shai-Hulud and the game was going to be about hunting units or the other way around, you are the Harvester escaping the Sand Worm. I decided to go for the second just because the game was more clear in my mind and more related with the feeling I wanted to give to the player.

Basically, in the game you control a Harvester and have to harvest Spice, at some point a Sand Worm tries to eat you and you have to escape and repeat that cycle until you harvest enough spice or until you get eaten.

### General

One thing I like in all my projects is to create different scenes to work on new things in isolation and start small each time. Initially the scene contains a visual mockup of what I want to create or implement and and later I start adding different test cases that allow me to complete what I want. These test cases are normally root objects that I enable or disable in order to work on an specific thing.

For example, when starting working on how the wheels projection on the terrain (more on that later) I created a scene named WheelTerrainPositioning and inside it there are multiple root objects with the test cases.

<div class="post-image">
    <a href="/assets/ldjam52-mockup-scenes.png"><img src="/assets/ldjam52-mockup-scenes.png" /></a>
     <span>The list of scenes I have in the game, most of them to develop specific feature.</span>
</div>

<div class="post-image">
    <a href="/assets/ldjam52-mockup-scenes-02.png"><img src="/assets/ldjam52-mockup-scenes-02.png" /></a>
     <span>The root objects for each test case inside the development scene.</span>
</div>

This is something I do for a long time now and I really like it to attack each feature in isolation and then integrate it with the rest of the game. I have to admit that I need to improve root objects naming, when it is clear it is an automatic test, I use something like `ExpectedBehaviour_When_Something`, but when I am not sure, I just put whatever.

### The Dunes

The first thing I wanted to achieve was the sensation of Arrakis’ dunes and for that I decided to use Unity’s terrain tool which I never used before and I quickly managed to have something that I liked. This was a pretty direct step, I just used the Raise or Lower terrain with default brushes and created what I wanted, and just modified a bit the Material to get a decent sand feeling.

<div class="post-image">
    <a href="/assets/ldjam52-terrain-01.png"><img src="/assets/ldjam52-terrain-01.png" /></a>
</div>

There were different things I wanted to try but had no time, one of them was to create the terrain using height maps and to randomize that each time you start the game and also to use that information for the Spice locations. 

Another thing that I tested a bit was the terrain grass, I wanted in my head to spawn the Spice like it was grass on the ground but with orange tones (like Spice). I felt it like was going to be a great idea but couldn't get it to look how I wanted at first and also I didn't know how to remove it after harvesting so I decided to avoid that. 

UPDATE: While writing the blog post I tried again and managed to make it look how I wanted, still don't know how to remove it from terrain though (now I want to investigate that). 

<div class="post-image">
<video width="480" height="270" controls>
  <source src="/assets/ldjam52-harvester-grass.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>Using the grass terrain tool to show the Spice in the sand.</span>
</div>

UPDATE: I managed to modify terrain grass in runtime while writing the blog post modifying the detail layer of the terrain (can write more detail if someone interested).

<div class="post-image">
<video width="480" height="270" controls>
  <source src="/assets/ldjam52-harvester-harvestnew.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>Modifying the terrain grass in runtime to give an idea of harvesting spice.</span>
</div>

In the end I just created a Game Object with an orange quad for each Spice grain and spawned hundreds of them in each location :grimacing:. It doesn't look so cool but anyways.

<div class="post-image">
    <a href="/assets/ldjam52-terrain-02.png"><img src="/assets/ldjam52-terrain-02.png" /></a>
</div>

Another thing I wanted to try but had no time was to spawn the Spice using different shapes and also based on the Terrain height so, mixed with creating different height maps and spawning based on that will give nice looking Spice locations (at least in my head were looking nice). 

### The Harvester

Controlled by the player, the Harvester is the main character of the game and it must harvest Spice while surviving the Sand Worm attacks. 

I started by creating a cube and moving it using the CharacterController component, which I never used before, and to see what happened. The first issue I had was that it was competing with RigidBody physics and I didn't remember they aren't meant to be use together, so when I tried to move the Vehicle it started rolling and couldn't control it. To fix that I removed the RigidBody.

To move, I calculated the desired motion given the input and a configurable speed and then applied to the CharacterController. The next problem I had was that it doesn't apply gravity by default, so the vehicle started to fly after moving over a slope. The quick fix for that is to include gravity in the motion vector when calling the Move() method. It does consider collisions with the ground so your character doesn't fall.

The next thing I did was to add "wheels" (just some rotated black cylinders). After playing with it a bit I wanted the wheels to be over the terrain, otherwise they were floating most of the time. 

In order to implement that, I used Physics RayCast against objects in the Terrain layer to detect the ideal position and then apply it to the Wheel transform. That worked fine for the Wheels but when using physics with terrain patches temporary culled or disabled (probably to improve performance) doesn't detect collisions, so for spawning Spice grains in far away locations it doesn't work. The fix for that is to use 
SampleHeight() instead of using physics, which always work and it's probably faster. I ended up making a solution mix since I also want to detect positions based on Game Objects in the Terrain layer too and for that I needed physics.

<div class="post-image">
<video width="480" height="270" controls>
  <source src="/assets/ldjam52-harvester-wheel-01.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>Project the wheel over the terrain using SampleHeight().</span>
</div>

After having that, I wanted the harvester to incline based on the wheel axis, so if moving uphill it will look up and the camera will consider that. In order to do that I calculated the normal of the vector difference between the front and back wheel axis and then rotated the vehicle in its X axis using that value.

<div class="post-image">
<video width="480" height="270" controls>
  <source src="/assets/ldjam52-harvester-wheel-02.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>Calculating normal based on front and back axis.</span>
<span>Note: I released the game with this turned of by mistake :facepalm:</span>
</div>

Finally, for the wheels traces I used a Line Renderer that spawns points based on the current wheel position and only after moving a minimum distance from the last spawned position. 

### The Mini Map

To create a game in two days, you have to take a lot of shortcuts and make a lot of hacks, and the Mini Map is an example of that.

I don't know the proper solution for a Mini Map since I never had to do something like that before. My approach in this case was to create a secondary camera that follows the Harvester far from above and to render it to a RenderTexture and show that texture in the UI.

<div class="post-image">
    <a href="/assets/ldjam52-minimap-camera.png"><img src="/assets/ldjam52-minimap-camera.png" /></a>
</div>

In order to control what to render, I created a GameObject with a colored Quad inside all the elements of the game I wanted to be shown in the map and set them in special layer named Scanner. 

Both the Scanner and and the Terrain layers are configured in the Mini Map camera Culling Mask. So, I added those GameObjects to the Harvester, the Sand Worm and each of the Spice grains :see_no_evil: so the player can see all that information in the map. I had to scale them up in order to see them in the map since I moved the camera far far away to see more. 

<div class="post-image">
    <a href="/assets/ldjam52-minimap-icons.png"><img src="/assets/ldjam52-minimap-icons.png" /></a>
</div>

And finally, to render that in the screen, I used a UI Raw Image with the RenderTexture set and configured the size I wanted to show it.

<div class="post-image">
    <a href="/assets/ldjam52-minimap-ui.png"><img src="/assets/ldjam52-minimap-ui.png" /></a>
</div>

Even if it wasn't a horrible approach, there are probably better ways to render that Mini Map texture with more control of what to show without having to create tons of GameObjects, but it was a great approach for the jam.

### The Sand Worm

The main thing I wanted to achieve with the Sand Worm was the moment it eats your Harvester. The first thing that came to my mind was an image of different rings of teeth rotating at different speed (don't know if it was from the movie) and that was my focus. So basically the worm will travel hidden in the dunes, visible in the Mini Map, and it will came out when it si below the harvester.

<div class="post-image">
    <img src="/assets/ldjam52-sandworm-attack.gif" />
    <span>Sand Worm attack sequence</span>
</div>

To create the model I used the ProBuilder tool. 

For the body I used a Pipe modified to make the mouth a bit bigger and then a set of Cones for the teeth. 

<div class="post-image">
    <a href="/assets/ldjam52-sandworm-01.png"><img src="/assets/ldjam52-sandworm-01.png" /></a>
    <span>The Game Object used for the Sand Worm.</span>
</div>

<div class="post-image">
    <a href="/assets/ldjam52-sandworm-02.png"><img src="/assets/ldjam52-sandworm-02.png" /></a>
    <span>Each element of the Sand Worm.</span>
</div>

For the teeth rotation I just used a script that rotates the euler angles given a speed and the delta time and I configured different speeds for each ring, and put that in the parent GameObject of the group of teeth.

Finally, for the devour sequence, I used a Cinemachine Virtual Camera that is turned on during the sequence and turned off after (if the Harvester is still alive).

### Tools and technologies used

Sometimes people ask me which language and/or technologies I used to make a game and I realized I normally forget to share that. So, here is a summary:

* Unity Engine
  - Terrain Tool
  - URP (Universal Render Pipeline) with Post Processing effects like ToneMapping, Bloom and Vignette.
  - Probuilder to build shapes
  - Cinemachine to make the camera follow the character and also for the devour sequence.
  - CharacterController and New Input System to control the character
  - Plugins
    - MyBox
    - Leantween
    - Vertx for better physics debugging
* C# language with Rider IDE
* Screen2Gif, [Floom](https://cokeandcode.com/floom/) and OBS to record videos and gifs.

## Conclusion

For a game I created in two days (I started one day late) and maybe in a total of 12 hours, I am quite happy with the results and all the generated knowledge. I had some doubts at first since I knew I had one day less and the other days I was going to have interruptions (I am a father of two and we are on holydays) but now I know I want to repeat it. 

One thing I realized was that I wanted to reuse code and tools from other projects and it wasn't easy, I ended up manually copying some code. I just confirmed that I need to start moving more of my code to decoupled projects I could easily reuse when working on new projects, jams or prototypes. For example, to use [MyBox package](https://github.com/Deadcows/MyBox.git) I just added the git url to the Package Manager and voilá, I need more of that.  

If you liked the blog post, [follow me on twitter](https://twitter.com/arielsan) and [share it](https://twitter.com/arielsan/status/1612526561181196297
), that will be much appreciated. 

Also, you can play the game at my [ichio page](https://arielsan.itch.io/spice-must-flow) or in the [Ludum Dare's Entry](https://ldjam.com/events/ludum-dare/52/spice-must-flow). If you want to take a look at the code, the game is [open source](https://github.com/acoppes/ldjam52/tree/release-for-ldjam52).

