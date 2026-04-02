---
layout: post
title:  "Design decisions when building games using ECS III"
date:   2026-03-29 00:17:00 -0300
excerpt: TODO 
tags:
  - ecs
  - unity
  - techniques
  - design
image:
  path: /images/ecs-post-preview.jpg
  height: 100
  width: 100
comments: true
---

TODO

TOPICS:

* GameObjectComponent
* EntityReference MonoBehaviour
* What Data is better to avoid in Components (refs, etc)
* Savegame in Ship Miner?
* Entity Definitions & Component Definitions (authoring?)


So, I decided to write the third part sooner than expected. This is the third part of the "how I am using ECS to make games?".

# GameObjectComponent

Even though I am using ECS and all my entities are pretty decoupled from Unity in some way, I am still using Unity GameObjects for multiple things. One of them is the for the visuals of the units and also the configuration of those visuals.

# Entity Definitions

Entity definitions are like the prefabs for entities. They are a basic API like, here you have an entity, configure it. But the main implementation is using GameObject prefabs and component definitions which are like the authoring for each component to be added to the entity when creating it.

My normal workflow to create an entity is to have a link to a definition and then do something like this:

```csharp
var spawnedEntity = world.CreateEntity(definition);
spawnedEntity.Get<SomeComponent>().something = value;
```

IMAGE OF AN ENTITY DEFINITION

Some interesting things of the GameObject implementation is that they support prefab variants, nested prefabs and local declarations, for example, the ship entity definition has a child object with the sfx definition for when the ship collides, which is referenced locally to be used when the ship collides with something and I want to spawn a new entity for the sound effect.

IMAGE OF THE SHIP DEFINITION

Another interesting thing is they can be used from prefabs, following the [GameObject as data pattern]() since they are just a declaration but they can also be used from a prefab instance in the scene with some modifications, super useful for my dev scenes where I want to test changes in the config to develop a new feature. For example, in the autorepair dev scene I added the regeneration component to the ship to see how it behaves, once I test a bit and validate, I apply it to the prefab.

IMAGE FOR THAT


-- TODO: link for the gameobject thing

# EntityPrefabInstance

This elements are GameObjects that allow me to automatically spawn entities from a definition and apply some normal overrides, for example: spawn a mining ship here but in enemy team and with extra health. They are pretty useful for stuff that is already there in the scene when the game starts, and super useful for the development scenes. But they can also be invoked in runtime as part of the game logic, for example: when the main player ship gets a new upgrade, then spawn some EntityPrefabInstance, the good part about this case is that they already have some configuration in that object. 

IMAGE OF ENTITY IN INSPECTOR AND IN SCENE