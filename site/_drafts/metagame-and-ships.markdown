---
layout: post
title:  "Metagame and replayability with new Ships"
# date:   2022-11-22 00:08:30 -0300
excerpt: To prepare for EA release I started working on adding more replayability through metagame unlocks. Having different ships sounds like a reasonable first thing to have in a game named "Ship" Miner. 
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

{{page.excerpt}}

<div class="post-image">
  <img src="/assets/shipminer/ships/shipminer-intro.gif" width="75%" />
</div>

# Introduction

In order to increase replayability, playing with a new ship should provide a different experience when playing the game again. That doesn't mean the core game should change but more like looking at the same game with different lenses. 

The approach I want is more like Dome Keeper or FTL where playing with unlocked content is optional and not like Wall World for example where you have to unlock metagame content in order to progress inside the game and in order to beat it.

# How to unlock the new Ships

My plan for unlocking ships is to complete different objectives, could be complete the game, complete a specific achievement or complete missions inside the game (same asteroid or not). 

However, since I don't have support for that right now, I suppose I initially would unlock them by completing asteroids or the game itself, and once I start having more content then improve how/when to unlock it. 

# First ships and thoughts on playing

Right now I am working on a couple of ships that will be there for next playtest version.

### Dual Rays (or the one that is the same but not the same for now)

One of them starts with double rays (now mining devices) and different initial stats, it is faster while not using the mining device but slower while using it and it is a bit more fragile.

* Fast
* Fragile
* Powerful

<div class="post-image">
  <img src="/assets/shipminer/ships/shipminer-dualrays.gif" width="75%" />
</div>

I didn't play too much with this Ship but I liked the double ray on start, it feels more powerful, and I like it moves faster than the other one. I still don't feel it so different than the main one but maybe it is ok since I needed a test of having a different "starting" values. 

However, I want to continue playing with its balance and with how the weapon behaves. I imagine one change could be that the rays do some attack pattern or increase a bit the speed penalty while using the mining device but make it really more powerful. Or maybe change it to be more like a electric/lighting charges that do some random spot attacks and some stat improvements could be to control it a bit better. 

### The Bomber 

The other one is a long range one, it fires mining bombs from the "mining devices" (not weapons!). It doesn't have movement penalty while using the mining device (at least for now) and it is a bit more resistant but it is slower in general. It has stats to upgrade for the bombs blast damage and radius.

* Slow
* Long range
* Area damage

<div class="post-image">
  <img src="/assets/shipminer/ships/shipminer-bomber.gif" width="75%" />
</div>

For this one, it feels the gameplay is already different, I played more from long distance and that changed a bit how I normally approach the asteroid. Mining the asteroid feels a bit more chaotic but also more powerful (might have some balance to do yet). It is easier to kill current big enemies since I am in a safe place most of the time and since it has no movement penalty I could dodge while charging attacks at the same time. I believe I need to nerf it a bit or find some interesting enemies that make the game harder in some situations with this ship.

### Other ideas to try

* The builder: it relies on building drones or mining stations (or something similar) close to the asteroid, could recycle them or move them with the main mining device.
* The driller: it is pretty good for mining in close distance, could be faster if going in straight line for example and/or needs to charge the drill and then can't change direction. Could also be more "active" ability type, where the main device charge does some kind of dash forward and that's how it mines differently from others.
* The bouncer: it accelerates fast and breaks the asteroid by hitting it, similar to dome keeper (and similar to one of the tech upgrades I have right now).

In all of them the core thing to have in mind is how they use the current controls of the game, what can I do as a player when using that ship. That's something more clear for some ships than others. 

One thing that happened while iterating on ships is that it feels that some of the current tech I have for the main ship doesn't work well for the other ships, so I deactivated them for those ships but also made me realize that maybe each ship could have its own special tech that could either maximize some of the ship's uniqueness or could minimize some of its weaknesses.

I like the driller as a really different version, maybe I change the double ray with that one and then share it in the playtest version and stop working for now. I will let it bake for a bit, wait for feedback, work on the rest of the game, get new ideas and/or inspiration from other things in the game, and then come back to add more ships, probably closer to the EA release and/or while in EA.

## Selecting ship screen (the hangar)

For the selection screen, I am not super sure what to do but I really like games where there is a mix of ingame and ui in order to do metagame actions, and in this case I created a selection level where you use a small shuttle ship and take control of the ship you want to use for the game. There you can experience a bit the basic stuff and see the stats, etc, and once you are decided, you can start the game.

<div class="post-image">
  <img src="/assets/shipminer/ships/shipminer-hangar-ships.gif" width="75%" />
</div>

For the future I still prefer having something like this but maybe adding some ui to show other information like the ship is locked and maybe show hint in how to unlock it or show the specific way to unlock it, for example: complete the game once.

## First game experience

Another thing to consider is not overwhelm the players showing too much from the beginning but at the same time show the game has different ships. I suppose one path here is to delegate that information to the trailer, steam page, description, etc, and then have an initial clean experience where the game goes directly to the ingame and doesn't show there are different ships or anything. 

However, I believe the best place to communicate what the game has is the game. So I might end up with a mixed solution like showing the hangar button but not allowing you to enter but still show some information like "Select different ships. Complete a small asteroid to unlock" when you press the button. Since I am still undecided I will wait to be closer to the EA release to define this.  

# The technical (boring) part of the blogpost

## Refactoring the game to support different ships

Most of the time adding new content makes me rethink how I am structuring the game but since it is the current objective and it is aligned with the vision and pillars of the game, it always feels like I am doing what I have to do. Also I am a refactor madman so I love when this happens.

In order to support having different ships I had to break a bit a lot of assumptions I did before where there was only one main ship and only one type. For example, the stats definitions were general and they had the max stats in the data asset. Now that each ship could have different stats and each stat could have a different max, I moved that information to the ships.

### Different mining devices

The main thing to do was to decouple the ships from the mining ray. The game until recently assumed the ship could only have mining rays. To do that I created the mining device intermediary concept to support having different implementations, for now I have the mining ray and the bombs launcher. That obviously implicated some other changes like how the stat upgrades and the installable technologies applies over the ship.

### Supported stats & technologies and max stats

For this I already moved information to each ship, to declare which stats they support and what is the max level they support, but I also started to do some changes to support how each stat level affect each ship, for one ship upgrading 1 stat could be increasing 20% of speed but for another could be 50%. That means I also have to modify the UI to support different values from each ship (I just remembered that xD).

In the case of the technologies, I started to filter which ones are supported or not since some of them don't make sense for some of the ships, so I only added support for filtering.

### Simplifying game configurations through plain text files

While working on creating the new ships I needed an easy way to visualize, compare and modify their values. For that reason, I decided to do something I had pending for a long time: to implement a way to configure units through a dedicated file (a JSON file in this case) similar to what [we did for Iron Marines](/2026/03/29/design-decisions-when-building-games-using-ecs2.html#how-we-did-configurations-in-iron-marines-invasion). I will save the details for another ECS blogpost.

## Conclusion

If you have ideas for different ships or unlockable content you want to see in the game, feel free to add a comment here or join discord to discuss there too. 

As always, Thanks so much for reading!