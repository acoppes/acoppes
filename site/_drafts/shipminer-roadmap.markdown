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

For those here for the first time, Ship Miner is a 1-bit Pixel Art twin stick miner game in space that I am developing mainly for Steam and Steam Deck (and would love to have it on consoles too).

<div class="post-image">
  <img src="/assets/roadmap/shipminer-gameplay-02.gif" />
</div>

If you like it you can add it to your wishlist here:

<div align="center">
<iframe src="https://store.steampowered.com/widget/3113690/?utm_source=personalpage&utm_campaign=announcement" frameborder="0" width="646" height="190"></iframe>
</div>

I recently did a recap of [what happened for me in 2024 and a high level plan for 2025](/2025/01/12/recap-2024-and-2025-plans/). Based on that, in this blogpost I want to write of what I think is the current state of Ship Miner and a possible roadmap for having a Demo in Steam.

# Current State of the Game

The core loop right now is to explore an asteroid and mine it in order to discover and gather valuable minerals. Those minerals allow the ship to improve by increasing stats or building and installing different technology to do the task faster and better and survive other challenges in the process. The objective (for now) is to find an artifact and charge it to destroy the Anomaly menace that is putting the galaxy in danger. 

The current version has the core experience pretty close to what I envision for the release, including game controls and core mechanics like moving, exploring and mining asteroids. Improving the ship is close to what I want but I feel it still needs a couple of iterations. The combat experience is not so bad considering the game in mainly about mining but it needs more content to make it more interesting.

The music is already great, the OST has a lot of songs and they feel more and more aligned with the target vibe of the game with each new song. In terms of sound effects, the game needs more and higher quality for a better experience, right now it has some generated with [sfxr](https://sfxr.me/) and others are voice recordings postprocessed in Audacity with some filters and effects (yes, you can hear me doing wooooosh and braaaams and stuff like that).

In general, the game needs more content and I already have a list of things to add.

I don't have a clear path defined for metagame, player progress and replayability and finding that is my current big objective, there are a [couple of ideas](#design-decisions-ahead) and I need to iterate a bit in order to find what is better for the game and what I can do.

_Note: If you are interested in playing the current version and want to help me with feedback, I have some Steam keys to give away. Or you could wait for the Demo too._

# Roadmap to Steam Demo

The objective of having a Demo is to share part of the game experience to capture the interest of more players by reaching a broader audience from within Steam itself. Also, it is a great opportunity to start getting more feedback in order to make the game better for the release (EA or final).

Defining the contents of the Demo should be a balance between doing the minimum amount of work that reflects the best of the game experience so I can publish the Demo as soon as possible. That is normally difficult but doing so while also defining part of the game will be even more.

What I know is that independently of the game decisions ahead, there are some things that are part of the experience that should be in a good state for the Demo. Here is a possible list of things:

* Basic Steamworks integration, at least having cloud save.
* Add to Wishlist moments like after finishing the game, in the main menu, etc.
* Show the game is going to have more content and what that content is, but wouldn't be available for the demo. This is to work on expectations but also to communicate what the game is going to have. 
* The game is running well in terms of performance right but I have to be sure it runs as smooth as possible for the demo too (I could use hacks here if something is not working so well).
* Maybe improve a bit the initial experience, maybe with a small tutorial.
* More and better sound effects.
* Other links like Discord, social networks, newsletter, etc.

# Roadmap to Steam Release

Here is a quick high level list of other things, not ordered, that will be part of the release (EA or final) and could be part of the Demo.

* More optimizations
* Better asteroid generation, right now the procedural generation is basic.
* Save and exit the game (continue where you were).
* High quality sound effects
* More content (this is more upgrades, enemies, etc)
* Steamworks integration (achievements, cloud save, etc)
* Localization in at least 4 languages, and have a base to extend that over time in next updates. I would like to release in English, Spanish (my main language), Japanese and Chinese.
* Tutorial
* Better options
* Retro visual effects like scanlines, crt, etc.
* Optional game modes

# Design Decisions

As I mentioned before, I have to decide how to proceed with the metagame, player progress and replayability.

When I started developing Ship Miner I imagined the game would have some kind of FTL selection screen where you decide which asteroid to travel next, selecting from different options that would change the experience, like more minerals but more enemies, or a less but more relaxing, etc. In each jump the player has to find a piece of the Artifact until, at some point, it is ready and try to beat the game with it. There could be also other game modes in some asteroids, like a another ship miner lost and you have to find it and provide help, or one where you have to find something and escape fast, etc, that was a interesting thing to have but it feels it would be go in another direction of the mining as core part of the game. 

Right now I am not sure about that game, it was kinda interesting but I believe I can manage to have part of the experience I wanted with that in one asteroid. I feel like I don't need to do the jump among asteroids as I initially thought and that would simplify some things too. At the same time, playing all the game in one asteroid allows me to do something I wasn't sure, that would be to have some "base building" experience where you have your base somewhere close to the asteroid, where you have to go back from time to time to install some tech or process something, etc. This path feels more aligned with using minerals and owning the asteroid in some way, and I could make bigger asteroids too.

Game changes with player decisions (TO-DO)

One thing that I feel right now is that the game starts to be a platform for games, and that's why I thought about having other game modes. I believe that's something I could explore later or during Early Access if I do that. I was even thinking at some point to make tools so the players could change/create other modes.

Thanks for reading!