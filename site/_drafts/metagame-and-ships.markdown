---
layout: post
title:  "Adding new Ships to Ship Miner"
# date:   2022-11-22 00:08:30 -0300
excerpt: To prepare for EA release I need to work on replayability and metagame. Having different ships sounds like a reasonable first step in a game named "Ship" Miner. 
author: Ariel Coppes
tags:
  - tag1
  - tag2
image:
  path: /images/ecs-post-preview.jpg
  height: 100
  width: 100
---

{{page.excerpt}}

# Introduction

In order to increase replayability, playing with a new ship should provide a different experience when playing the game again. That doesn't mean the core game should change but more like looking at the same game with different lenses. 

The approach I want is more like Dome Keeper or FTL where playing with unlocked content is optional and not like Wall World for example where you have to unlock metagame content in order to progress inside the game and in order to beat it.

## How to unlock the new Ships

My plan for unlocking ships is to complete different objectives, could be complete the game, complete a specific achievement or complete missions inside the game (same asteroid or not). 

However, since I don't have support for that right now, I suppose I initially would unlock them by completing asteroids or the game itself, and once I start having more content then improve how/when to unlock it. 

## First ships and thoughts on playing

Right now I am working on a couple of ships that will be there for next playtest version.

### The Flyer

One of them starts with double rays (now mining devices) and different initial stats, it is faster while not using the mining device but slower while using it and it is a bit more fragile.

* Fast
* Fragile
* Powerful

GIF

I didn't play too much with this Ship but I liked the double ray on start, it feels more powerful, and I like it moves faster than the other one. I still don't feel it so different than the main one but maybe it is ok since I needed a test of having a different "starting" values. However, I will probably continue playing with its balance and with how the weapon behaves. I imagine one change could be that the rays do some attack pattern or increase a bit the speed penalty while using the mining device but make it really more powerful. Or maybe change it to be more like a lighting charges that do some random spot attacks and some stat improvements could be to control it a bit better. 

### The Bomber 

The other one is a long range one, it fires mining bombs. It doesn't have movement penalty while using the mining device (at least for now) and it is a bit more resistant but it is slower. It has its own stats for the bombs blast damage and radius.

* Slow
* Long range
* Area damage

GIF

For this one, it feels the gameplay is already different, I played more from long distance and that changed a bit how I normally approach the asteroid. Mining the asteroid feels a bit more chaotic but also more powerful (might have some balance to do yet). It is easier to kill current big enemies since I am in a safe place most of the time and since it has no movement penalty I could dodge while charging attacks at the same time. I believe I need to nerf it a bit or find some interesting enemies that make the game harder in some situations with this ship.

### Other ideas to try

* The builder: it relies on building drones or mining stations (or something similar) close to the asteroid, could recycle them or move them with the main mining device.
* The drill: it is pretty good for mining in close distance, could be faster if going in straight line for example and/or needs to charge the drill and then can't change direction. Could also be more "active" ability type, where the main device charge does some kind of dash forward and that's how it mines differently from others.
* The bouncer: it accelerates fast and breaks the asteroid by hitting it, similar to dome keeper (and similar to one of the tech upgrades I have right now).

One thing that happened while iterating on ships is that it feels that some of the current tech I have for the main ship doesn't work well for the other ships, so I disabled them but also made me realize that maybe each ship could have its own special tech that could either maximize some of the ship's uniqueness or could minimize some of its weaknesses.

I like the driller as a really different version, maybe I change the double ray with that one and then share it in the playest version and stop working on having too much content here. Let it bake for a bit, wait for feedback, work on the rest of the game, get new ideas and/or inspiration from other things in the game, and then come back and add the rest of the ships, closer to the EA release or while in EA.

## Refactoring the game to support different ships

Most of the time adding new content makes me rethink how I am structuring the game but since it is the current objective and it is aligned with the vision and pillars of the game, it always feels like I am doing what I have to do. Also I am a refactor madman so I love when this happens.

In order to support having different ships I had to break a bit a lot of assumptions I did before where there was only one main ship and only one type. For example, the stats definitions were general and they had the max stats in the data asset. Now that each ship could have different stats and each stat could have a different max, I had to move that information to the ships.

### Different mining devices

The main thing to do was to decouple the ships from the mining ray. The game until recently assumed the ship could only have mining rays. So I had to create an intermediary concept which is the mining device and that device could be "anything" in the future, for now I have the mining ray and the bombs launcher. That obviously implicated some other changes like how the stat upgrades and the installable tech applies over the ship.

### Supported stats & technologies and max stats



* weapon system
* supported stats & technologies and max stats per ship
* selected ship to savegame
* changed main game to spawn the corresponding ship
* selecting the ship and testing screen

### Simplifying game configurations through plain text files

While working on creating the new ships I needed an easy way to visualize, compare and modify their values. For that reason, I decided to do something I had pending for a long time: to implement a way to configure units through a dedicated file (a JSON file in this case) similar to what [we did for Iron Marines](/2026/03/29/design-decisions-when-building-games-using-ecs2.html#how-we-did-configurations-in-iron-marines-invasion).

This is an example of how the part of the configuration looks:

```json
{
    "ship_main": {
        "_health": {
            "total": 3
        },
        "_ship_movement": {
            "rotation_speed": 540,
            "min_mult_by_direction": 0.5,
            "max_mult_by_direction": 1,
            "firing_speed_multiplier": 0.5
        },
        "_tractor": {
            "max_range": 1,
            "force_multiplier": 4
        },
        "_stats": {
            "speed": 3500,
            "vision_range": 3.5
        }
    }
}
```

As a side note, the main configurations file will be exported plain text with the game so it will be easy for anyone to edit to play with different values and see how the game changes.

I would love to write about the technical details and decisions but will save that for the next post about my game framework + ECS.

<!-- I decided to have some basic systems to autoconfigure the components, for those I reserve keys starting with `_`, so for example `_tractor` is automatically detected by the configuration system, but then the keys like `ship_main` are things I use in the main ship definition to identify it wants that configuration. -->

## Ships selection screen (the hangar)

Not super sure what to do here but I really like the games where there is a mix of ingame and ui in order to do metagame actions, and in this case I did a hangar screen where you move using a shuttle ship and take control to the ship you want to play, and you can check the stats and test flight a bit the ship in the game itself. 

In the future I believe I still prefer something like this but might mix more with ui to show some other information, for example, show a ship is locked until some mission is completed (or maybe keep that hidden, not sure about that one).


## First time

Another thing to consider is not overwhelm the players showing too much from the beginning but at the same time show the game has different ships. So I suppose one path here is to delegate that information to the trailer, steam page, description, etc, and then have an initial clean experience where the game goes directly to the ingame and doesn't show there are different ships or anything. However, I am one of those developers that believe the game is the best place to communicate so maybe I end up with a mixed thing here, like showing the hangar button but not allowing you to enter, show some information like "Select different ships. Complete a small asteroid to unlock". Also, I suppose there should be an unlocked option the first time you unlock the hangar.  

## Conclusion

If you have ideas for different ships or unlockable content you want to see in the game, feel free to add a comment here or join discord to discuss there too. 

As always, Thanks so much for reading!