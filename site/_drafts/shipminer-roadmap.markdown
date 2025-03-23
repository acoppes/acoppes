---
layout: post
title:  "Ship Miner's Roadmap to Demo"
date:   2025-01-01 00:00:00 -0300
excerpt: In this blogpost I want to write of what I think is the current state of Ship Miner and a possible roadmap for having a Demo in Steam.
author: Ariel Coppes
tags:
  - personal
  - update
  - review
  - shipminer
image:
  path: /images/2023-post-preview.jpg
  height: 100 
  width: 100
---

For those here for the first time, Ship Miner is a 1-bit Pixel Art twin stick miner game in space that I am developing mainly for Steam and Steam Deck.

<div class="post-image">
  <img src="/assets/roadmap/shipminer-gameplay-02.gif" />
</div>

You can wishlist and read more info about the game here:

<div align="center">
<iframe src="https://store.steampowered.com/widget/3113690/?utm_source=personalpage&utm_campaign=announcement" frameborder="0" width="646" height="190"></iframe>
</div>

I recently did a recap of [what happened for me in 2024 and my plan for 2025](/2025/01/12/recap-2024-and-2025-plans/) and in this blogpost I want to write more about the plan for this year. In particular, what I think is the current state of Ship Miner and a possible roadmap for having a Demo in Steam.

# Current State of the Game

The core loop right now is to move around an asteroid to explore and mine it in order to discover and gather valuable minerals. Those minerals allow the player to improve the ship by increasing stats or building and installing different technology to the task faster and better and survive other dangers in the process. The objective (for now) is to find an artifact and charge it to destroy the Anomaly menace once and for all. 

The current version has the core experience pretty close to what I envision for the release, including game controls and core mechanics like moving, exploring and mining asteroids. Improving the ship is closer to what I want but I feel it still needs a couple of iterations. And the combat experience is not so bad considering the game in mainly about mining but it needs a lot of content to make it more interesting.

The music is great already, the game has a lot of songs and feels more and more aligned with the target mood with each new song. In terms of sound effects, the game needs more and higher quality for a better experience, right now it has some generated with [sfxr](https://sfxr.me/) and others are voice recordings postprocessed in Audacity with some filters and effects (yes, you can hear me doing wooooosh and braaaams and stuff like that).

In general, the game needs more content, I have a list of things to implement already but was focused in trying to close the game loop until now.

I don't have a clear path defined for metagame progress and replayability and that is my current big objective, there are a couple of ideas and I need to iterate a bit in order to define what is better for the game and for what I can deliver.

If you are interested in playing the current version and want to help me with feedback, I have some Steam keys to give away (or you could wait for the Demo).  

# Roadmap for Demo

The objective of having a Demo is to show a bit more of the game experience but also to get more interest in the game from within Steam itself and get some public feedback in order to help me make the game better for the release.

Defining the contents of the Demo should be a balance between doing the minimum amount of work that reflects the best of the game so I can publish the Demo as soon as possible to start capturing interested players. That is normally difficult but doing so while also defining the game will be even more.

What I know is that independently of the decisions ahead, I want a good experience and that implies some stuff like:

* Basic Steamworks integration: cloud save.
* Optimizations: The game should run as smooth as possible
* Basic tutorial to make the initial experience a bit more seamless (could be improved for the release)
* A longer experience than what I have right now that reflects what the game is going to be but feels but makes sense for a demo.

the closest deliverable would be a Demo. to share the game a bit more and hope to get more wishlists but from within steam. Maybe even join some of the Steam Fest but I believe it is better to join one of those closer to  a release. For that Demo, I have to define what it could include, obviously it is difficult because one could start to include as much as possible to share the best experience but if you add everything to the demo, then is that a demo or release?

# Other things in the Roadmap for release

Here is a list of other things that might be part of the Demo or not, but will be for sure be part of the release (final or EA).

* Optimizations
* Save and exit the game (continue where you were) and could save.
* Sound Effects 2.0
* More content
* Tutorial + Steamworks integration
* Localization

### Travel to different asteroids during a run

Right now the game is all in one asteroid, I want to add deciding and traveling to different asteroids but at the same time I am not sure if the game needs it. The main idea is to pick the destination based on some information, similar to FTL, like more dangerous but high in one mineral, and also to allow me to grow in by creating different game modes like removing permanent fog reveal or having to do something different like saving another ship miner in trouble, etc.

### Upgrades 2.0

Define how they are unlocked and improved. There is a version right now where you kinda level up by mining and randomly unlock stuff but it is a temporary thing, one of the ideas in mind is to give the player some decision when unlocking, from a pool of upgrades/things to build, like each time you level up the game presents some options between unlocking a new upgrade, upgrading an upgrade you already have or unlocking a buildable, or anything else. 

### Save and exit the game

Want to define if the game should have or not savegame ingame, that means to leave the game in the middle of a run and comeback later to continue. Why? because maybe it if each asteroid mining is short enough, it could be like a commitment to enter the game and play for 5 to 10 mins. But at the same time I am a parent of two that can't play games for more than 5 mins sometimes xD, and it is cool if you can just turn off the game and come back later and just continue where you were at.

### Sound Effects 2.0

Right now the game has some basic sound effects generated by [jsfxr](https://sfxr.me/) or by my recording my voice and applying some filters. With Esteban (Unfold), the music composer, we are going to integrate FMOD and give him as much control as possible to improve and make more professional sound effects.

### Procedural generation 2.0

The game right now has only procedural generation on minerals and other content in the level but not the asteroid structure/layout, the idea is to work in that part generating different layouts to increase the value in exploring the asteroid.

### Metagame and Player Progression

Define how deep I want to go with metagame. I will have basic things for sure in terms of unlocking achievements and maybe information about the world but I want to define if the player is also gonna unlock stuff to help changing the game over time, like enter the game with some upgrades unlocked already or maybe changing the ship to have another configuration, etc.

### More Content & Options

This is an ongoing objective, but the game needs more content for sure in order to be released and something I will work on during all development. And also in terms of options like changing controls,

### Performance Optimizations

Even though the game looks like it is not heavy, it processes a lot of systems and will process more, so I have to work on this point during all the development too.

### Tutorial, Steamworks & Demo

Game is clear in some points but not so much in others, once I work a iterate a couple of times and define some missing points, I will create a Tutorial to help introduce the complex things of the game to new players.

And this one too but if not, it is mainly integrate with the Steamworks SDK in order to have things like cloud save and achievements, among others. 

And also think about publishing a Demo of the game that could help in getting more wishlists and prepare for the EA release.

### Multiple Languages

Last but not least, create the platform to support multiple languages and also start adding those over time, the idea is to release in EA with at least a couple of languages and grow with more languages for the final release of the game.

## To conclude

This blog post was mainly to share what happened in 2024 and the plan for 2025. A lot could change but I believe the main idea will be the same.

Thanks for reading!