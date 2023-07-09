---
layout: post
title:  "Thinking solutions in ECS paradigm when making games"
# date:   2022-11-22 00:08:30 -0300
excerpt: For some time I am using one ECS solution or another when making games, from Artemis when I used LibGDX to a custom ECS for Iron Marines 2, then Unity ECS for a nda project and now Leopotam for the games I am working right now. Independently the specifics of the solutions, by using ECS I slowly changed my way of thinking when making games, I want to share my experience so far. 
author: Ariel Coppes
tags:
  - personal
  - update
---

{{page.excerpt}}

I already shared some insights about ECS in [Gemserk Blog](https://blog.gemserk.com), but this one will try to be more specific and with more real examples.

First, a small recap, ECS stands for Entity Component System, is both a paradigm (you have to change how you solve things) and an structure that allows performance improvements. Basically an Entity (just an identifier) has one or more Components (data) and there are Systems that execute logic over a entities with a set of components. 

EXAMPLE OR LINK TO EXAMPLE HERE

Even though Systems implement the logic, they depend directly on which components the entity has and their data. This means the behaviour of the application in ECS is mostly data driven: adding or removing a component or chaning its data will change which systems execute and what they do. 

For example, in the game I am workin right now:

EXAMPLE ABOUT MODEL AND SPINE?

Systems are good to define general logic, things that you want to execute over any entity that matches the criteria of components. For example, in order to simulate gravityScale when using Unity Physics 3d I have a GravitySystem that adds gravity acceleration to entities with the GravityComponent. 

THINKG A BETTER GAME EXAMPLE

However, there are cases where you need specific logic that only one entity at a specific time of the game should do. In those cases I like to use some kind of scripting solution[^1], similar to MonoBehaviours, things that only run for one entity.

Scripting
- reuse controller instances
- data stored in entities through components or through blackboard component
- real examples from the game
- the examples from the endless/platformer blogpost are in controllers/scripts

[^1]: When we used LibGDX and Artemis at Gemserk we created our [scripting system](https://blog.gemserk.com/2011/11/13/scripting-with-artemis/), it felt like oviously necessary to customize logic.

Some times what logic should be in a System and what logic should be in a Script is not so obvious, and that is something I want to talk about in this blog post. .

