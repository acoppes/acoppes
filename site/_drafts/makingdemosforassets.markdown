---
layout: post
title:  "Making demos for assets (WIP)"
# date:   2024-01-18 20:00:00 -0300
excerpt: This blogpost is about the process of making penusbmic assetspacks demos.
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

I am pretty happy with what I am doing right now. I am mainly focusing on Cleared Hot and at some point in the future I really want to make a blog post about how we are working, what we are doing, and how fun and enjoyable is to work on this project. I am also making assets demos with penusbmic for some of his assets packs (which I am going to talk more in this blogpost) and from time to time I work a bit on Ship Miner too.

## Assets Pack Demo

We already implemented two playable units (which were meant to be enemies), and they look like this:

<div class="post-image">
    <img src="/assets/penusbmicdemos/dusk-jumper-01.gif" width="370px" />
</div>

<div class="post-image">
    <img src="/assets/penusbmicdemos/dusk-warrior-01.gif" />
</div>

I am now working on integrating also the main hero of that asset pack, and it looks like this already:

<div class="post-image">
    <img src="/assets/penusbmicdemos/stranded-hero-01.gif" width="370px"/>
</div>

So, I just implemented them, what is the interesting part of this blog post?

## High level process

Well, the general high level process is simple, penusbmic did a character (already released or not), he sends the character to me (already done or almost done), and I implement that in the demo. 

I start by getting the assets and think some ideas on what the abilities should be, if it is easy to implement a base idea I do that quickly and share some gifs with him and we discuss and align on the ideas. Normally we are almost 100% aligned so my initial interpretations on the character and animations are what it ends up being implemented.

We could normally iterate a bit in the middle (if he is still working on the character, before releasing it) to adjust something (for example, adding a charge animation in the case of the Dusk Warrior), or if it is 100% done (like the hero), I just manage to make something interesting with what it is there.

## My internal process

Internally, for these assets I reuse a lot of what I had implemented with all the mini games I did last year (handle input, handle animations, movement, leave footsteps, some particles, fire projectiles, camera shakes, etc), so making demos for this kind of assets is super straightforward to me. But still, there are some decisions to make and some code to write, and that might be interesting to someone? lets find out.

# Dusk Jumper

The assets normally came with everything in one aseprite file, the character + visual effects + projectiles + decals, etc. 

<div class="post-image">
  <iframe width="480" height="270" src="http://www.youtube.com/embed/OiM3iBJrWm8" frameborder="0" allowfullscreen></iframe>
  <span>This is the first time I try a video to explain something</span>
</div>

So, the first step, for all assets, is to do a manual pre process (unless I convince penusbmic otherwise xD) of the asset to separate stuff that is not going to be together in runtime. I have experience with this from when I was at Ironhide Games, so I can visualize what I need to separate and how in order to implement the Character with his abilities.

This is the list of things I do to export it to be used:

* Make the animations look to the right (some characters, mainly enemies start looking the to other side).
* Center the asset in the pivot point (I consider the shadow center normally the pivot).
* Remove the background.
* Separate in parts: character, each projectile, each visual effect, each decal. This can be done in different files or not.
* Depending the abilities, split animations, for example if I want to make a charged attack. 

*Note: this depends a lot on MY process, but might be as inspiration for yours.*

For this character, we have normal idle/walk and then we have two abilities, one that charges and fires a projectile far away (depending the charge) and one to jump outside the screen and then fall when/where you want and performing a slam (damage) when it touches the ground (+ some camera shake of course).  

* Separate assets and objects in multiple parts (effect should be there if unit is not)

# Dusk Warrior

* Do damage in order to feel the attacks (added some bushes)

# Stranded Hero

* Since it is a hero, get deeper, it should feel better (added acceleration/deceleration, walk/run anims, attack movement, allow interrupt anims, etc)

# Conclusions

Why I wanted to share this blog post? well I am getting fun implementing this characters which have a lot of depth already in the concept and animations and for some reason I wanted to share it with the world. 

As a recap of my process from getting the asset to implement it=?


I am also testing sharing videos explaining some stuff, but I want to still share that in the blog post but I believe that showing a video helps in getting some ideas more clear.



