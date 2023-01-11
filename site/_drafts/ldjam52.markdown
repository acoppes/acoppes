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

### General

When starting new projects I like to create different scenes, initially with visual mockup of what I want ot attack next and later with different test cases that allow me to implement the feature. For example, when starting working on how the wheels project to the terrain (more on that later) I created a scene named WheelTerrainPositioning and inside it I have multiple root folders with different test cases.

<div class="post-image">
    <a href="/assets/ldjam52-mockup-scenes.png"><img src="/assets/ldjam52-mockup-scenes.png" /></a>
     <span>The list of scenes I have in the game, most of them to develop specific feature.</span>
</div>

<div class="post-image">
    <a href="/assets/ldjam52-mockup-scenes-02.png"><img src="/assets/ldjam52-mockup-scenes-02.png" /></a>
     <span>The root objects for each test case inside the development scene.</span>
</div>

This is something I do for a long time and I really like to attack each feature in isolation, even though I have to improve root object naming.

### The Dunes

The first thing I wanted to achieve was the sensation of Arrakis’ dunes and for that I decided to use Unity’s terrain tool which I never used before and I quickly managed to have something I liked. This was a pretty direct step, I just used the Raise or Lower terrain with default brushes and created what I wanted, and just modified a bit the Material to get a decent sand color.

<div class="post-image">
    <a href="/assets/ldjam52-terrain-01.png"><img src="/assets/ldjam52-terrain-01.png" /></a>
</div>

There were different things I wanted to try but had no time, one of them was to create the terrain using height maps and to randomize that in different runs so you have to discover different Spice locations. 

Another thing I tested a bit was grass painting, I wanted in my head to spawn the Spice like it was grass on the ground but orange, I felt it like was going to be a great idea but couldn't get it to look how I wanted and also I didn't know how to remove it after harvesting so I decided to avoid that. 

<div class="post-image">
<video width="480" height="270" controls>
  <source src="/assets/ldjam52-harvester-grass.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>Using the grass terrain tool to show the Spice in the sand.</span>
</div>

So in the end I just created a Game Object with an orange quad for the Spice grain and spawned hundreds of them in each location :grimacing:, not so cool.

<div class="post-image">
    <a href="/assets/ldjam52-terrain-02.png"><img src="/assets/ldjam52-terrain-02.png" /></a>
</div>

Another thing I wanted to try but had no time was to spawn the Spice using different shapes and also based on the Terrain height so, mixed with creating different height maps and spawning based on that will give nice looking Spice locations (at least in my head were looking nice).

Anyways, in the end I just did the basics with the terrain.

### The Harvester

Controlled by the player, the Harvester is the main character of this game and it must harvest Spice while surviving the Sand Worm attacks. 

I started by creating a cube and started moving it using the CharacterController component, which I never used before, and to see what happened. The first issue I had was that it was competing with RigidBody physics and I didn't know they aren't meant to be use together (at least not at the same time), so when I tried to move the Vehicle it started rolling and couldn't control it. The fix for that was to remove the RigidBody.

To move, I calculated the desired motion given the input and a configurable speed and then applied to the CharacterController. The next problem I had was that it doesn't calculate gravity by default, so the vehicle started to fly after moving over a slope. The quick fix for that is to include gravity in the motion vector when calling Move() method to the CharacterController. 

The next thing I did was to add "wheels" (some rotated cylinders). After playing with it a bit I wanted the wheels be over the terrain all the time. In order to implement that I just used Physics RayCast against objects in the Terrain layer to detect the terrain position and then apply it to the wheel. That worked but using physics with terrains has an issue for terrain temporary excluded from the physics simulation (probably to improve performance), wasn't the case of the wheels but I used the same logic for other stuff and it failed. The fix for that is to use the proper SampleHeight() API instead of using physics, which always work and probably faster. However, I had other normal Game Objects marked as Terrain so ended up making a solution mix.

<div class="post-image">
<video width="480" height="270" controls>
  <source src="/assets/ldjam52-harvester-wheel-01.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>Project the wheel over the terrain using SampleHeight().</span>
</div>

After having that, I wanted the harvester to incline based on the wheel axis, so if moving uphill it will look up and the camera will consider that. In order to do that I calculated the normal of the vector difference between the front and back wheel axis and then applied that to the object.

<div class="post-image">
<video width="480" height="270" controls>
  <source src="/assets/ldjam52-harvester-wheel-02.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>Calculating normal based on front and back axis.</span>
</div>

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

Finally, for the devour sequence, I used a Cinemachine Virtual Camera that is turned on during the sequence and turned off after (if the Harvester is still alive).

### What I used to make the game

Sometimes people ask me which language and/or technologies I used to make a game, and I though not so frequent I share an extensive list, here it is:

* Unity and C#
  - Terrain Tool
* URP (Universal Render Pipeline)
* Post processinig effects like bloom, vignete, etc.
* Probuilder
* Cinemachine
* New Input System

## Conclusion

For a game I created in two days (I started one day late) and maybe in a total of 12 hours, I am quite happy with the results and all the generated knowledge. I had some doubts at first since I knew I wasn't going to spend the first day and I was going to work some sparse time (I am a father of two) but now I know I want to repeat it. 

One thing I realized was that I wanted to reuse code and tools I have in the game I am making but it wasn't easy, I ended up copying some code. I confirmed that I need to start moving my code to public and shared projects in some way to have it easy to use when starting new projects, for jams or for other prototypes. For example, I wanted to use the [MyBox package](https://github.com/Deadcows/MyBox.git) and it was just adding the git url to the Package Manager.  

By the way, if you liked the post and the game, [follow me on twitter](https://twitter.com/arielsan) and [share](https://twitter.com/arielsan/status/1612526561181196297
), and also play the game at my [ichio page](https://arielsan.itch.io/spice-must-flow) or in the [Ludum Dare's Entry](https://ldjam.com/events/ludum-dare/52/$319699). And if you want to take a look at the code, the game is open source [at my Github](https://github.com/acoppes/ldjam52).

