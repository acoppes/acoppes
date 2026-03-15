---
layout: post
title:  "Taking time for experiments"
# date:   2022-11-22 00:08:30 -0300
excerpt: From time to time I t
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

For Cleared Hot we are using [A* Pathfinding Project](https://arongranberg.com/astar/documentation/stable/index.html) for agents navigation and focused on performance improvement we decided to switch to use the agent version built over Unity ECS (FollowerEntity) since it was recommended for that reason, and that made me get my interest in Unity ECS back.

Some years ago, when Unity ECS was starting I used it in a couple of projects. I used the 0.0.14-preview version for a personal prototype named [Naive Network Game](https://github.com/acoppes/NaiveNetworkGame) and also used the 0.0.51-preview version for a cancelled platformer/sidescroller project at Ironhide Game Studio (last project I worked on before leaving).

In both occasions I was super excited learning about the specific Unity solution but I found some limitations and ended up doing hacks and workarounds that I didn't like so when I started my own journey I decided to go with a less powerful but more direct approach by using the Leopotam solution.

Now, after recently getting my interest in Unity ECS back, and in order to refresh my memory and relearn, I decided to do an experiment and upgraded my prototype from ecs 0.0.14-preview to ecs 1.4.

# The Project

Naive Network Game is a remote pvp version of an old prototype I had named Tiny Warriors where the main idea was to take rts macro decisions like invest in army, invest in workers, build, attack or defend, and the rest is like an idle game almost.

SCREENSHOT

# Migrating to new ECS

Translation/Rotation => LocalTransform

FROM DELEGATES TO FOREACH

FROM TO ENTITY TO AUTHORING

MIGRATING SYSTEMS USING CLAUDE WEB
  - defined the rules, migrated systems
  - it did the work faster than me (maybe 15 mins vs 3-4hs manual work), and pretty well
  - it lied to me super confident
  - it invented enums that didn't exist
  - it removed important code even though I asked to not touch that code, and it took me some time to find out what happened

VIDEO SHOWING THE GAME WORKING

The main idea was to now have a more interesting playground where I can experiment with new ecs stuff from now on.
