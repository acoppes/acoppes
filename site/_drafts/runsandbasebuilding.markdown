---
layout: post
title:  "Ship Miner: Making runs feel more different and indirect combat."
date:   2026-07-15 00:08:30 -0300
excerpt: Preparing the game for release, I am tackling some big issues and unknowns to then focus on adding more content and polishing the game. In this blogpost in particular I want to talk about making runs feel more different and having more indirect combat.
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

One of the issues is that the game feels a bit repetitive in some decisions during the run, reducing replayability. This doesn't mean there are no interesting decisions, in fact, there are even lots of implicit decisions around the core gameplay but the explicit ones need more work. For example, which technology to install in the ship is a matter of time and randomness but you end up installing everything and it feels like there is no real decision there after playing multiple times.

The other issue is that the game combat is too centered around the ship and it feels like it needs more combat oriented actions like dashing or blocking but that was never my intention, it is a "relaxing" mining game with combat, not a combat game with mining, so that not the direction I want for the solution.

## Current planned idea/solution

To improve first issue the idea is to start digging deeper in the roguelike elements. This means having more optional unlocks during runs, probably when collecting blueprints or traveling to other asteroids. Those unlocks could be technologies, buildings, global upgrades, etc, but it will presented more in a roguelike manner, where you have to select one of multiple choices, and they will unlock paths but might block others. In order to support that, the game needs more content. 

For the other issue, the idea is to remove the ship as center of the combat by adding more automatic elements like buildings, drones and ships, each one with different attacks, and delegate the real combat to them. Some of them could even be extensions of the main ship that automatically react to enemies.

## Micro base building 

Implementing everything takes time, I prefer to do it in steps, and decided to start with some fo the indirect elements, in particular, the buildings which I call _space stations_. 

-- tower defense
-- GIF
-- 


## Conclusion

-- TODO

As always, Thanks so much for reading!

<div align="center">
<iframe src="https://store.steampowered.com/widget/4028800/?utm_source=personalpage&utm_campaign=devlog" frameborder="0" width="646" height="190"></iframe>
</div>