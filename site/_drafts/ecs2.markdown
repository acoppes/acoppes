---
layout: post
# title:  "I got my interest on Unity ECS back"
title:  "Design decisions when building games using ECS II"
# date:   2022-11-22 00:08:30 -0300
excerpt: This is just the second part to the "Design decisions when building games using ECS" since I have new patterns that I want to share. 
tags:
  - ecs
  - unity
  - techniques
  - design
image:
  path: /images/ecs-post-preview.jpg
  height: 100
  width: 100
---

Even though the previous [previous part](/2023/07/13/design-decisions-when-building-games-using-ecs.html) was super complete (now that I read it again), I have a couple of new examples that I want to share related with how I am using ECS (entity component system) approach when making games.

# Scripting System

As I mentioned before, I am using ECS for Ship Miner (and my other prototypes). In particular I am using [leoecs-lite](https://github.com/LeoECSCommunity/ecslite) as the ECS solution and I built my own systems and tools over it. 

One important system I have is the Scripting System, and it allows me to have a way to have custom logic for my entities but using the same components. I named my scripts `Controllers` (all the related systems contains that keyword in their name).

For example, the Repair Drone script looks something like this: 

```csharp
public class RepairDroneController : ControllerBase, IUpdate, IStateChanged, IInit, IDamagedEvent, IDestroyed
{
    public float healingPerSecond = 1;

    public float totalHealing;
    private float currentHealing;

    [EntityDefinition]
    public Object rayDefinition;

    private Entity rayEntity;

    public bool autodestroyOnComplete;
    
    // ... CODE ...

    public void OnUpdate(World world, Entity entity, float dt)
    {
        ref var states = ref entity.Get<StatesComponent>();
        ref var abilities = ref entity.Get<AbilitiesComponent>();
        ref var shipMovement = ref entity.Get<ShipMovementComponent>();
        ref var animations = ref entity.Get<AnimationsComponent>();
        
        var healing = abilities.GetAbility("Healing");
        var findHealingTarget = abilities.GetAbility("FindHealingTarget");
        
        // ... CODE ...

        if (currentHealing >= totalHealing)
        {
            healing.Stop(Ability.StopType.Completed);
            states.ExitState("Healing");
            return;
        }

        // ... CODE ...
    }

    // ... CODE ...

}
```

TODO: SHOW SCREENSHOT OF CONTROLLER CONFIG (PREVIOUS EXPLAIN HOW CONTROLLERS WORK)

This is super specific to this unit, it checks for targets to repair inside some range, if it finds one, it locks on a target and decides to move there, once it is close it activates a ray to repair the target, and then disables the ray for some time.

The idea for the script is to be the brain and to reuse as much as possible all the components that already exists. 

# The Implementation OF THE SYSTEM

-- TODO: write about the system in general --

# Using Stateless Controllers

In my previous post I talked about my process when [I didn't know where I should put new logic](/2023/07/13/design-decisions-when-building-games-using-ecs.html#my-process-when-i-dont-know-the-best-place-for-code-or-logic) if it should be a system or a controller. Now, after using that workflow multiple times, another thing that I learned is that, when I decide to go with a Controller, I should always try to keep them as stateless as possible, and it is usually simple to do it.

In the previous example, the repair drone has both configuration values like `healingPerSecond` or `rayDefinition` (that's ok, could be better) and runtime values, like the `currentHealing` or the `rayEntity`. Having runtime values there is an issue for the future, and here are some of the reasons:

* It is not easy accessible from other controllers nor systems if needed for other logic.
* For the same reason and also because data in controllers tends to not follow any common structure, it is harder to debug. When data is in Components it is easier to debug both because it normally follows a more consistent data structure and also because it is easy to iterate in all components.
* I can't reuse the same Controller instance, I need to have an instance per entity in that case, while for stateless Controllers I can make entities reuse the same instance. 
* It is harder to serialize in case I want save the state of an entity or send it over the network.

What I try to do now is, first, try to see if I can store the data I want in an existing Component that make sense. For example, if I am creating some cooldowns to execute some action, maybe I can channel that using other Components I already have, like the [AbilitiesComponent](https://github.com/acoppes/unity-gemserk-utilities/blob/main/Packages/com.gemserk.gamescore/Components/Abilities/AbilitiesComponent.cs). If I can't, I just create a new Component, could be a "general" Component if it makes sense, or could be a specific Component. In the case of the Repair Drone, I could create a specific Component like this one: 

```csharp
public class RepairDroneComponent : IEntityComponent {
    public float currentHealing;
    public Entity rayEntity;
}
```

And then either add that Component as part of that Entity Definition or add that Component dynamically in the Controller `Init()` and then use it by accessing the `entity.Get<RepairDroneComponent>()` inside the Controller's logic.

## What about the configuration values?

Well, having configuration values it is a bit less troublesome since normally the values are kinda constant but still have some issues:

* If I want different configuration values, I need for sure different Controller prefabs for each config, so I can´t reuse the Controller instance, but it is less bad since I can reuse per entity type normally.
* It makes less clear where the entity definition configuration is, sometimes is in Components, sometimes in the Controller, and I have to explore multiple times. 

To improve this, I can either move those values to a new Component, for example RepairDroneConfigurationComponent or could even be the same Component I use for the runtime data if I want to. Another option could be to move it to a separated configuration concept (we used that in Iron Marines 2).  

# React to general events in Scripts (Controllers) 

# The Event Component

# TOOLS LIKE THE ECS DEBUGGER (EXTENDED VERSION OF THE LEOECS)

