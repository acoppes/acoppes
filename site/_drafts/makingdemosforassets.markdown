---
layout: post
title:  "Making demos for assets (WIP)"
# date:   2024-01-18 20:00:00 -0300
excerpt: This blogpost is about the process of making penusbmic assetspacks demos, from high level to some implementation details.
author: Ariel Coppes
tags:
  - personal
  - update
  - review
  - nontechnical
image:
  path: /images/2023-post-preview.jpg
  height: 100
  width: 100
---

{{page.excerpt}}

While working on [Cleared Hot](https://store.steampowered.com/app/1710820/Cleared_Hot/) (wishlist now!!) and [my own project](https://shipminer.arielcoppes.dev/), I also have a side project of making playable demos with [penusbmic](https://penusbmic.itch.io/) for some characters from his [Stranded Series](https://itch.io/c/3652990/stranded-series) assets pack. 

## Assets Pack Demo

We have three characters implemented, two enemies and a hero:

<div class="post-image">
    <img src="/assets/penusbmicdemos/dusk-jumper-01.gif" width="370px" />
    <span>Dusk Jumper: fires projectiles and jumps and slams foes.</span>
</div>

<div class="post-image">
    <img src="/assets/penusbmicdemos/dusk-warrior-01.gif" />
    <span>Dusk Warrior: spins in a multidirectional attack cutting everything in his path.</span>
</div>

<div class="post-image">
    <img src="/assets/penusbmicdemos/stranded-hero-01.gif" width="370px"/>
    <span>The Hero: walks, runs and perform combo attacks while also rolling to a evade attacks. This character has more depth in terms of movement and animations directions.</span>
</div>

You can **[play the demo at itch](https://arielsan.itch.io/penusbmic)**.

## High level iteration

Well, the general high level process is simple, penusbmic has a character (already released or not), he sends the character to me (already done or almost complete), and I implement that in the demo. 

I start by getting the assets and think some ideas on what the abilities should be, if it is easy to implement a base idea I do that quickly and share some gifs or videos with him and we discuss and align on the ideas. For example, I see an attack I believe it should charge while holding a button and perform after releasing it, I do that (no code for the damage or anyhing, just play animations) and if he agrees with that, I continue.

Since the assets are pretty visual and clear, we are normally pretty aligned on the direction to follow so my initial interpretations of the character and animations are what I end up implementing.

For some cases we iterated a bit in the middle to adjust something, for example, adding a charge animation in the case of the Dusk Warrior, or adding the position indicator in the case of the Dusk Jumper. In the case of the Stranded Hero, which was already done and published, I just managed to make something interesting with what it is there.

## From the asset to the implementation

For these demos I reuse the framework I implemented for the mini games I did the last year (input handling, animations, movement, footsteps, particles, projectiles, camera shake, etc) to simplify the task, in fact, I even used penusmbic assets packs for them so I am used to integrate them. But still, there are some things to decide and make in order to implement this characters.

### Integrating Dusk Jumper

*Disclaimer: this depends a lot on MY personal process, but might be as inspiration for yours.*

The assets normally came with everything in aseprite files, the character + visual effects + projectiles + decals, etc. And also come with some spritesheet png files for each animation, it depends, it is not always the same criteria.

*Note: in order to have a control on the assets you need to have [aseprite](https://www.aseprite.org/).*

Since I normally integrate everything from aseprite itself (I have a custom Unity importer), I do a manual pre process step to separate elements that are not going to be together in runtime, but the idea is applicable in general. Separating everything doesn't mean I create an aseprite file for each element, sometimes it makes sense to have them together in one aseprite file.

*I based this process on the experience from when I was at [Ironhide Games](https://www.ironhidegames.com/), that applies well to these asset packs.*

For this character, we have normal idle/walk and then we have two abilities, one that charges and fires a projectile away (distance depends on charge time) and one to jump outside the screen and then fall when/where you want and performing a slam (damage) when it touches the ground (+ some camera shake of course). And it also has a death animation. 

This is the list of elements for this character:

* Character
* Jump indicator (when the character is outside the screen)
* Bomb
* Bomb explosion
* Bomb explosion decal
* Slam ground effect
* Slam ground effect decal

This is the list of things I do to export it to be used:

* Make the animations look to the right (some enemy characters look to the left in the original file).
* Center the asset in the pivot point (I consider the shadow center normally the pivot).
* Remove the background (which is normally used to create the gifs and/or to see how some things look while designing the character and animating it).
* Separate in parts: character, each projectile, each visual effect, each decal. Again, this can be done in different files or not.
* Depending the abilities, split animations, for example if I want to make a charged attack I might create 3 different animations from the attack, the start, the loop and the end.

<div class="post-image">
  <iframe width="480" height="270" src="http://www.youtube.com/embed/OiM3iBJrWm8" frameborder="0" allowfullscreen></iframe>
  <span>Here is a video showing the initial asset and some of the assets I created from it. Btw, this is the first time I do a video to explain something</span>
</div>

SOME IMPLEMENTATION DETAILS?

### Stranded Hero

The hero has not a lot of variety in terms of attacks but gets a bit deeper in terms of animation directions and also has a walk and run separated animations, so for this one I had to change a bit some rules.

* Since it is a hero, get deeper, it should feel better (added acceleration/deceleration, walk/run anims, attack movement, allow interrupt anims, etc)

# Conclusions

Why I wanted to share this blog post? well I am getting fun implementing this characters which have a lot of depth already in the concept and animations and for some reason I wanted to share it with the world. 

As a recap of my process from getting the asset to implement it=?


I am also testing sharing videos explaining some stuff, but I want to still share that in the blog post but I believe that showing a video helps in getting some ideas more clear.



