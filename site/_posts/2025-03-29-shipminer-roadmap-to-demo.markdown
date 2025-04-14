---
layout: post
title:  "Ship Miner's Roadmap to Demo"
date:   2025-03-29 00:08:00 -0300
excerpt: In this blogpost I want to write of what I think is the current state of Ship Miner and a possible roadmap for having a Demo in Steam.
author: Ariel Coppes
tags:
  - personal
  - update
  - review
  - shipminer
image:
  path: /images/shipminer-preview.png
  height: 100 
  width: 100
---

For those here for the first time, Ship Miner is a 1-bit Pixel Art twin stick miner game in space that I am developing mainly for Steam and Steam Deck (and would love to have it on Nintendo Switch and other consoles too).

<div class="post-image">
  <img src="/assets/roadmap/shipminer-gameplay-02.gif" />
</div>

If you like it, you can add it to your wishlist here:

<div align="center">
<iframe src="https://store.steampowered.com/widget/3113690/?utm_source=personalpage&utm_campaign=announcement" frameborder="0" width="646" height="190"></iframe>
</div>

I recently did a recap of [what I did in 2024 and a high level plan for 2025](/2025/01/12/recap-2024-and-2025-plans/). In this blogpost I want to write of what I think is the current state of Ship Miner and a possible Roadmap for having a Demo in Steam.

# Current State of the Game

The core loop of the game is to explore an asteroid and mine it in order to discover and gather valuable minerals. Those minerals allow the ship to improve by increasing stats or building and installing different technology to do the task faster and better and survive other challenges in the process. The objective (for now) is to find an artifact and charge it in order to destroy the Anomaly menace that is putting the galaxy in danger. 

The current version has the core experience pretty close to what I envision for the release, including game controls and core mechanics like moving, exploring and mining asteroids. Improving the ship is close to what I want but I feel it still needs a couple of iterations. The combat experience is not so bad considering the game in mainly about mining but it needs more content to make it more interesting.

The music is already great, the OST has a lot of songs and they feel pretty aligned with the target vibe of the game. In terms of sound effects, the game needs more and higher quality for a better experience, right now it has some generated with [sfxr](https://sfxr.me/) and others are voice recordings postprocessed in Audacity with some filters and effects (yes, you can hear me doing wooooosh and braaaams :laughing:).

In general, the game needs more content like enemies, technology, etc.

I don't have a clear path defined for metagame, player progress and having a good replay value. Finding and deciding that is my current objective. I have a [couple of ideas](#design-decisions-ahead) that I need to analyze more and iterate in order to find what is better for the game.

In general, sharing videos and gifs of the game is doing well on Twitter/X and Bluesky and even [Threads](https://www.threads.net/@ariel_coppes/post/DG9QtsgRdqk) started to work better recently.

<div class="post-image">
  <img src="/assets/roadmap/threads-insights.png" width="50%" />
</div>

The game has 1.8k wishlists right now and I will keep working on marketing to reach 2k soon, I hope :relaxed:.

# Roadmap to Steam Demo

The objective of having a Demo is to share part of the game experience to capture the interest of more players by reaching a broader audience from within Steam itself. Also, it is a great opportunity to start getting more feedback in order to make the game better for the release (EA or final).

Defining the contents of the Demo should be a balance between doing the minimum amount of work that reflects the best of the game experience so I can publish the Demo as soon as possible. That is normally difficult but doing so while also defining part of the game will be even more.

What I know is that, independently of the game decisions ahead, there are some things that are part of the experience that should be in a good state for the Demo, and here is a list:

* Basic Steamworks integration, at least having cloud save.
* Add calls to action to wishlist the game, for example, after completing the demo, in the main menu, etc.
* Show the game is going to have more content and what that content is. This is to work on expectations but also to communicate what the game is going to have. 
* The game is running well in terms of performance right now but I have to be sure it runs as smooth as possible for the Demo (more considering I expect people to play it on the Steam Deck).
* More and better sound effects.
* New trailer
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
* Better options
* Retro visual effects like scanlines, crt, palette swap, etc.
* Optional game modes

# Next Steps

As I mentioned before, I have to decide how to proceed with the metagame, player progress and having a good replay value.

When I started developing Ship Miner I imagined the game would have some kind of FTL selection screen where the player decides which asteroid to travel next, selecting from different options that would change the experience, like more minerals but more enemies, or a less but more relaxing, etc. In each jump the player has to find a piece of the Artifact until, at some point, it is ready and try to beat the game with it. There could be also other game modes in some asteroids, like there is another ship miner that needs help or one where you have to find something and escape fast, etc. This is a possible path for the game where each different run will provide different choices and that would give the player different experiences.

Another path in mind is to have all the game to be in one asteroid, no traveling to others. I believe most of the experiences I wanted could be adapted, there is no real need for traveling, and this could simplify some things and open the door to others, like having some kind of micro "base building" experience where you have your base somewhere close to the asteroid and you can build new tools there and/or have to go there to install technologies, or process some minerals, etc _(note: after thinking this a bit, it could also be added the travel asteroids option)_. This path feels more aligned with using minerals and owning the asteroid in some way, and I could make bigger asteroids too. In this case the experience in the asteroid should change with each play, and could evolve based on the player progress _(but that can happen with the travel asteroids option too)_. 

Independently the path, there are some ideas I want for the game that could be worked on anyways. One is having some big things that change the gameplay in a temporary or permanent way, for example, the reveled fog could start to regenerate or you only have local vision, or an star storm starts hitting the asteroid doing damage to everything. The main idea is to provide ways of changing the general gameplay and experience. 

Another thing to try is to change the game parameters like playing in a smaller or bigger asteroid, more or less resistant, with more or less minerals density, etc. I will probably start by adding some kind of customization screen to detect what is more interesting to change or not, but it is in some way something important for both paths. 

Improving the procedural generation of the asteroid will benefit the game in general too, and I will try to work on this as part of my next steps too.

Once decided, the Demo could have part of this that reflects the idea and experience but doesn't need to have all the content. The main thing here is to communicate as best as possible what the final game will be.

On a side note, the game is starting to feel like a platform for games and I believe that's something I will might explore more after the release.

_Note: If you are interested in playing the current version of Ship Miner and want to help me with your feedback, I have some Steam keys to give away, just send me an email. You could also wait for the Demo if you prefer._

Thanks for reading!