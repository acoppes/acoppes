---
layout: post
title:  "Adding new Ships to Ship Miner"
# date:   2022-11-22 00:08:30 -0300
excerpt: To prepare for EA release I need to continue working on metagame and replayability and having different ships sounds like a reasonable step in a game named "Ship" Miner. 
author: Ariel Coppes
tags:
  - tag1
  - tag2
image:
  path: /images/ecs-post-preview.jpg
  height: 100
  width: 100
---

## Intro 

{{page.excerpt}}

The main objective with having different Ships is to increase replayability. In order to achieve that, the ships should be different enough so the experience of playing the game again doesn't feel the same. 

However, the game will not need the different unlockable content to be completed like Wall World for example, but provide different ways of experiencing the game, more similar to FTL, where you have options of starting the game in different conditions.

## How to unlock the new Ships

This is something I still didn't define but would be either by completing the game, playing multiple times and/or completing specific achievements or missions in the game. I like the third option but right now I don't support for anything like that so I suppose initially it would be after completing asteroids or the game.

## First ships and thoughts on playing

GIF

Right now I have a couple of new ships, one starts with a double ray and different stats, it is faster while not mining but slower while using the mining devices and also it is a bit more fragile. 

GIF

The other one is a long range one, it fires mining bombs through the mining device, it doesn't have movement penalty while charging the shoot (at least for now) and it is a bit more resistant but it is normally slower. It has its own stats for the bombs blast damage and radius.

Some ideas for new ships include:

* Drilling device one, which mines from closer distances, could be faster for going in straight line and might be faster while not mining to escape from enemies or something.
* Drones based one, could spawn drones to mine, could h 

## Refactoring the game to support different ships

Most of the time adding new content makes me rethink how I am structuring the game but since it is the current objective and it is aligned with the vision and pillars of the game, it always feels like I am doing what I have to do. Also I am a refactor madman so I love when this happens.

In order to support having different ships I had to break a bit a lot of assumptions I did before where there was only one main ship and only one type. For example, the stats definitions were general and they had the max stats in the data asset. Now that each ship could have different stats and each stat could have a different max, I had to move that information to the ships.

* supported stats & technologies and max stats per ship
* weapon system
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