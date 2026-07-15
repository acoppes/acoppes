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

_As a side note, the main configurations file will be exported plain text with the game so it will be easy for anyone to edit to play with different values and see how the game changes._

I believe I will write more about this and the technical details in the next post about ECS and how I am making games.

<!-- I decided to have some basic systems to autoconfigure the components, for those I reserve keys starting with `_`, so for example `_tractor` is automatically detected by the configuration system, but then the keys like `ship_main` are things I use in the main ship definition to identify it wants that configuration. -->


# The technical part of the blogpost (FROM THE SHIPS BLOGPOST)

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