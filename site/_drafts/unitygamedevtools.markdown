---
layout: post
title:  "My GameDev Tools are Open Source from the beginning"
# date:   2024-01-18 20:00:00 -0300
excerpt: When I started my gamedev journey at the beginning of the previous year, one important objective in mind was to work in building a set of tools and utilities while developing each game, prototype, etc, in order to be reused for the next ones, and improve and grow it each time. 

author: Ariel Coppes
tags:
  - tools
  - technical
image:
  path: /images/2023-post-preview.jpg
  height: 100
  width: 100
---

{{page.excerpt}}

For that purpose I created this Github project named [Unity Gemserk Utilities](https://github.com/acoppes/unity-gemserk-utilities) (Gemserk was my previous blog/company). 

This set of tools is meant to be used mainly by me and for my own games but there are some useful things that could be handy for someone else and that is why I prefer to have it open source. 

One interesting thing is that, once I started working in [Cleared Hot](https://store.steampowered.com/app/1710820/Cleared_Hot/) at the end of the last year, we decided to integrate and use some of these tools. This is awesome since it allows me to adapt and improve them by using it in different contexts and also start to get feedback from more developers.

## Unity Triggers' Logic

The main project we are reusing is named Unity Triggers' logic and it is heavily inspired in Starcraft 2 Editor. The idea is something like this: there are Triggers which are executed once an Event happened, and if a set of Condition are validated, a list of Actions are executed.

<div class="post-image">
    <img src="/assets/unity-triggers-diagram.png" />
    <span>Basic diagram of how it works.</span>
</div>

The implementation for this over Unity uses GameObjects and is pretty simple.

<div class="post-image">
    <img src="/assets/unity-triggers-gameobjects.png" />
    <span>Each GameObject has a Event, Condition or Action MonoBehaviour.</span>
</div>

The core is simple, the main idea with this system is the custom events, conditions and actions you implement for the game you are making, and from Cleared Hot we already have a lot, some for development, some for testing, and most of them for the logic of the levels. 

<div class="post-image">
    <img src="/assets/unity-triggers-gameobjects-clearedhot.png" />
    <span>Some triggers for a mission in Cleared Hot.</span>
</div>

Some of the generic elements we created in Cleared Hot, for example a condition to check if an GameObject has a tag or not, could be integrated in the core project to make them useful for more people and/or for our own projects in the future.

This was an simplified view of the Triggers' Logic, might do a more in depth look in another blog post.

## Common Utilities

The other one we are using is the our Unity Utilities, 

As example, one useful thing we have right now is ObjectType attribute with its picker window

GIF IN ACTION

We are using this to select Spawnables ....

I am also using it for my own projects, and for Ship Miner in particular, when selecting Definitions (a topic to talk in another blog post maybe).

That's all for now, just wanted to do a simple blogpost more focused on techincal stuff since I didn't do that in a while and I am kinda missing it.

Thanks for reading!!



