---
layout: post
title:  "Building my own gamedev tools and utilities, from the start, for the future"
date:   2024-09-15 08:00:00 -0300
excerpt: When I started my gamedev journey at the beginning of the previous year, one important objective in mind was to work in building a set of tools and utilities while developing each minigame, prototype, experience, etc, to be used for the next ones, and so on. 

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

For that purpose I created an open source repo named [Unity Gemserk Utilities](https://github.com/acoppes/unity-gemserk-utilities) (Gemserk was my previous blog/company name, might rename to Pixel Core Utilities in the future?). This repo consist in a set of projects with different levels of abstraction and for various usages.

This set of projects is meant to be used mainly by me and for my own games but there are some useful things that could be handy for someone else and that is why I prefer to have it open source, just in case I want to share something, it is just there. 

In fact, when I started working in [Cleared Hot](https://store.steampowered.com/app/1710820/Cleared_Hot/), at the end of the last year, we decided to integrate and use some of these projects. That allows me to adapt, abstract, and improve those projects by using them in different contexts and by getting feedback from other developers.

## Unity Triggers' Logic

The main project we are using is named Unity Triggers' Logic and it is heavily inspired by the Starcraft2 Editor. Triggers are a set of Events, Conditions and Actions and when one of the Events is triggered, a set of Conditions is evaluated if all of them pass then a list of Actions is executed. We used something pretty similar for Iron Marines and Iron Marines 2, and I wrote about that in the [Gemserk devblog](https://blog.gemserk.com/2017/03/27/playing-with-starcraft-2-editor-to-understand-how-a-good-rts-is-made/). 


A pseudo example:

```
  When Unit1 Enters Area3, 
  
  if 
    Unit1 IsAlive 
    and 
    Unit1 HasWeapons

  Spawn GroupOfEnemies1 
  Move Camera to Area3
```

Here is a screenshot of how triggers look in the SC2 Editor:

<div class="post-image">
    <img src="/assets/unity-triggers-sc2example-crop.png" />
    <span>SC2 Trigger Editor.</span>
</div>

The implementation for this over Unity uses GameObjects and MonoBehaviours and is pretty simple.

<div class="post-image">
    <img src="/assets/unity-triggers-gameobjects.png" />
    <span>Each GameObject has an Event, Condition or Action MonoBehaviour.</span>
</div>

The core is simple, the main idea with this system is the custom events, conditions and actions you implement for the game you are making, and from Cleared Hot we already have a lot of them, some for development, some for testing, and most of them for the logic of the levels. Here is an example of a work in progress mission:

<div class="post-image">
    <img src="/assets/unity-triggers-gameobjects-clearedhot.png" />
    <span>Some triggers for a mission in Cleared Hot.</span>
</div>

One interesting thing is, since they are GameObjects, we can use them as prefabs too. This comes with all the normal prefab features, for example, prefab instance modifications, prefab variants, nested prefabs and reuse the same prefab in different scenes, etc.

Some of the generic elements we created in Cleared Hot, for example a condition to check if an GameObject has a tag or not, could be integrated in the core project to make them useful for more people and/or for our own projects in the future.

## Common Utilities

The other one we are using in Cleared Hot and Ship Miner is the Unity Utilities project. It contains several utilities but one that I like a lot is the Object picker, a c# attribute and a custom editor window that in combination allow the selection of Objects (GameObjects or Assets), from scene or from assets folder, implementing some API, in the case of GameObjects it looks for at least one MonoBehaviour implementing it, in the case of assets it checks for implementing the API directly.

```csharp
[ObjectType(typeof(IEntityDefinition), disableAssetReferences = false)]
public Object entityArchetype;
``` 

<div class="post-image">
    <img src="/assets/unity_utilities_objecttype_picker.gif" />
    <span>Object picker allows you to select GameObjects or Assets implementing an API directly or in a MonoBehaviour.</span>
</div>

In the case of my games there is a concept for entity archetypes named Entity Definition used when to new entities in the ECS system (it might be an interesting topic to talk in another blog post).

That's all for now, just wanted to do a simple blog post with a bit of technical content since I didn't do that for a while and I was missing it.

Thanks for reading!!



