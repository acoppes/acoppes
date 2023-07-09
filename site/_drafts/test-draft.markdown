---
layout: post
title:  "Changing my mindset to make games using Entity Component System frameworks"
# date:   2022-11-22 00:08:30 -0300
excerpt:  When using an ECS, what data goes in which Component, what logic goes in which System, when using a Scripting framework, what logic goes in scripts, etc, this blogpost tries to cover, with real examples, my experience with these decisions over the years of using different ECS frameworks in different games. 
author: Ariel Coppes
tags:
  - personal
  - update
---

{{page.excerpt}}

# Background

My experience with ECS goes back to 2010 when we started making games at Gemserk with [@rgarat](https://twitter.com/rgarat), we used [Artemis](https://blog.gemserk.com/2011/11/13/scripting-with-artemis/) at that time to make games and to make the port of Clash of the Olympians. Over the years I used different ECS frameworks for different games and I even developerd my own for [Iron Marines Invasion](https://www.ironmarinesinvasion.com/)[^1]. Now, I use the Community maintained fork of [LeoECS](https://github.com/LeoECSCommunity) for my current games.

In ECS, An Entity, which is just an identifier, can have one or more Components, holding the data. Systems have the logic to process entities with a set of Components in order to transform the data from one state to another.

For me, ECS is both a different programming paradigm (you have to change how you solve things) and a way to structure the code in order to achieve big performance improvements, different frameworks might go deeper in this aspect but all share the basics. 

# Where to put the data

# Where to process the logic

# Tips to decide



EXAMPLE OR LINK TO EXAMPLE HERE

Even though Systems implement the logic, they depend directly on which components the entity has and their data. This means the behaviour of the application in ECS is mostly data driven: adding or removing a component or chaning its data will change which systems execute and what they do. 

For example, in the game I am workin right now:

EXAMPLE ABOUT MODEL AND SPINE?

Systems are good to define general logic, things that you want to execute over any entity that matches the criteria of components. For example, in order to simulate gravityScale when using Unity Physics 3d I have a GravitySystem that adds gravity acceleration to entities with the GravityComponent. 

THINKG A BETTER GAME EXAMPLE

However, there are cases where you need specific logic that only one entity at a specific time of the game should do. In those cases I like to use some kind of scripting solution[^2], similar to MonoBehaviours, things that only run for one entity.

Scripting
- reuse controller instances
- data stored in entities through components or through blackboard component
- real examples from the game
- the examples from the endless/platformer blogpost are in controllers/scripts

Some times what logic should be in a System and what logic should be in a Script is not so obvious, and that is something I want to talk about in this blog post. .

[^1]: When we used LibGDX and Artemis at Gemserk we created our [scripting system](https://blog.gemserk.com/2011/11/13/scripting-with-artemis/), it felt like oviously necessary to customize logic.

[^2]: Yes, I tried Unity ECS multiple times during development, it wasn't ready for what I wanted and it was hard to explain to new developers at Ironhide, that is why I decided to avoid it.