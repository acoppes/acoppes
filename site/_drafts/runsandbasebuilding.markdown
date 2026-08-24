---
layout: post
title:  "Ship Miner: Making runs feel more different and indirect combat."
date:   2026-08-24 00:08:30 -0300
excerpt: While I am preparing the game for EA release, I am tackling some big issues and unknowns to then focus on adding more content and polishing the game. In this blogpost in particular I want to talk about making runs feel more different and changing the combat to be more indirect.
author: Ariel Coppes
tags:
  - development
  - gamedesign
  - metagame
image:
  path: /images/ecs-post-preview.jpg
  height: 100
  width: 100
---

<div class="post-image">
  <img src="/assets/shipminer/shipminer-gameplay_02.gif" width="75%" />
  <span>This is Ship Miner btw, <a href="https://store.steampowered.com/widget/4028800/?utm_source=personalpage&utm_campaign=devlog">Demo available on Steam!</a></span>
</div>

# Introduction

{{page.excerpt}}

One of the issues is that the game feels a bit repetitive in some decisions during the run. This doesn't mean there are no interesting decisions, in fact, there are even lots of implicit decisions around the core gameplay but the explicit ones need more work. For example, which technology to install in the ship is a matter of randomness but you end up installing everything and it feels like there is no real decision there after playing multiple times.

The other issue is that the game combat is too centered around the ship and it feels like it needs more combat oriented actions like dashing or blocking but that was never my intention since it is a "relaxing" mining game with combat, not a combat game with mining, so that's not the direction I want for the solution.

## Current planned idea/solution

To improve first issue the idea is to start digging deeper in the roguelike elements. This means having more optional unlocks during runs, probably when collecting blueprints or traveling to other asteroids. Those unlocks could be technologies, buildings, global upgrades, etc, but they will presented more in a roguelike way, where you have to select one of multiple choices, and they will unlock paths but might block others. 

For the other issue, the idea is to remove the ship as center of the combat by adding more automatic elements like buildings, drones and ships, each one with different attacks. Some of them could even be extensions of the main ship.

## Micro base building 

As first step I decided to start by implementing indirect elements to build, the _space stations_.

To build stations there are some _invisible holders_ around other stations that become visible and interactable when the ship is near, and support different build options. Once the station is built over the holder, that one disappear and now there are new ones around the first one.

<div class="post-image">
  <img src="/assets/shipminer/stations/shipminer-stations-holders.gif" width="75%" />
</div>

As part of the first iteration I added three utility stations and one defensive station. The utility stations use part of the content I already had, for example, the mining station spawns mining drones over time. The other ones are the repair station, which spawns repair drones and the scan station which spawns the drones that explore the asteroid and find minerals.

<div class="post-image">
  <img src="/assets/shipminer/stations/shipminer-stations-mining.gif" width="75%" />
</div>

The defensive station attacks enemies when they are close to the base, the default one just fires projectiles similar to the bomber ship (I also reused part of the content here to have the general idea working first).

<div class="post-image">
  <img src="/assets/shipminer/stations/shipminer-stations-defensive.gif" width="75%" />
</div>

Having stations to build also helps in having more options to spend minerals on. It also gives a small glimpse of a micro base building / tower defense feeling. 

Current stations have no options to improve or change but that is something I might work in further iterations.

This is available in private beta already.

## Conclusion

Next step for the solution is to start unlocking game content and global upgrades through the run. With global upgrades I mean for example something like adding +1 drone to all mining stations, or extra speed to all drones from all stations. For in asteroid unlocks I might use blueprints and for others I might use the asteroid destination selection. 

However, my next iteration is going to be about localization since I want to be prepared and to iterate with the localization team. Part of the iteration will be to validate Japanese and Simplified Chinese work properly with the font I selected and/or what should I do to support them for launch. A possible fallback here could be to switch the UI to be more HD and just use a common known font to support these languages. For localization in general, the idea is to have an open format simple for the community to add new languages if they want to.

Thanks so much for reading and remember to play the demo and/or wishlist the game! 

<div align="center">
<iframe src="https://store.steampowered.com/widget/4028800/?utm_source=personalpage&utm_campaign=devlog" frameborder="0" width="646" height="190"></iframe>
</div>