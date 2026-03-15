---
layout: post
# title:  "I got my interest on Unity ECS back"
title:  "Spending time in experimenting and learning"
# date:   2022-11-22 00:08:30 -0300
excerpt: I tried using Unity ECS some years ago but after finding some limitations (it was still an early version) I decided to switch to another ECS solution and forgot about it but now that 1.0 was released and more people are using it, I got my interest back and decided to start experimenting with it again.
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

We were recently improving Cleared Hot performance and one of the changes we did was to switch the astar navigation agents from the solution we are using [A* Pathfinding Project](https://arongranberg.com/astar/documentation/stable/index.html) to be Unity ECS version (FollowerEntity) to get some improvements there which forced us integrate Unity ECS to the project and after exploring the code, my interest back in Unity ECS started to come back.

NOTE: we can use jobs/etc to improve perf, not forced to use entities.

Some years ago, when Unity ECS appeared I was really excited about it and experimented with it in a couple of projects. I used [0.14.0-preview](https://docs.unity3d.com/Packages/com.unity.entities@0.14/manual/index.html) in a personal prototype named [Naive Network Game](https://github.com/acoppes/NaiveNetworkGame) and [0.50.1-preview](https://docs.unity3d.com/Packages/com.unity.entities@0.50/manual/index.html) in a cancelled platformer/sidescroller project at Ironhide Game Studio (last project I worked on before leaving).

In both occasions there were some interesting things but also limitations which lead me to do hacks and workarounds for things that should've been simple. Also, those versions didn't have good debug tools and a lot of the API was pretty unstable and changed all the time (in general it wasn't super hard to upgrade but you never knew when was the best time to do it). 

That experience wasn't horrible but it convinced me to try a less powerful but more direct approach to ECS like [LeoECSLite](https://github.com/LeoECSCommunity/ecslite) which I ended up using for my own projects, including Ship Miner.

Now, after recently getting my interest in Unity ECS back, and in order to refresh my memory and relearn, I decided to do an experiment and upgraded my prototype from ecs 0.0.14-preview to ecs 1.4.

# The Project

Naive Network Game is a remote pvp version of an old prototype I had named Tiny Warriors where the main idea was to take rts macro decisions like invest in army, invest in workers, build, attack or defend, and the rest is like an idle game almost.

VIDEO OF GAME

LIMITATIONS I FOUND

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

# Integrated FMOD

Another experiment I had pending for a long time was to integrate FMOD to Ship Miner and switch to use that audio system instead of the Unity one but I was delaying it because I thought it was going to take some time. However, after migrating that project to latest ECS, I decided to also use it to experiment with FMOD so I integrated it and used the example project sound effects to evaluate the process. 

It ended up being super easy but also during the process I noticed you don't have to fully replace Unity audio system with FMOD, you can have both running in parallel, and that was a revelation, I understood that I could integrate it in Ship Miner and convert it step by step without the need of doing it all in one move.

The main idea was to now have a more interesting playground where I can experiment with new ecs stuff from now on.
