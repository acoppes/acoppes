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

Even though the previous [previous part](/2023/07/13/design-decisions-when-building-games-using-ecs.html) was super complete (now that I read it again), I have a couple of new examples that I want to share. 

# Scripting System

As I mentioned before, I am using ECS for Ship Miner (and my other prototypes). In particular I am using [leoecs-lite](https://github.com/LeoECSCommunity/ecslite) as the ECS solution and I built my own systems and tools over it. 

One important system I have is the Scripting System, and it allows me to have a way to have custom logic for my entities but using the same components. I name my scripts `Controllers`, hence the system is `ControllerSystem`.

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

This is super specific to this unit, it checks for targets to repair in distance, it locks on a target and decides to move there, once it is close it activates a ray to repair the target, and then disables the ray for some time, etc.

The idea for the script is to be the brain and to reuse as much as possible all the components that already exists. 

# The Implementation OF THE SYSTEM

-- TODO: write about the system in general --

# Using Stateless Controllers

In my previous post I talked about my process when [I didn't know where I should put new logic](/2023/07/13/design-decisions-when-building-games-using-ecs.html#my-process-when-i-dont-know-the-best-place-for-code-or-logic) if it should be a system or a controller. Now, after using that workflow multiple times, another thing that I learned is that I should always try to keep controllers as stateless as possible, and it is not hard to do that.

In the previous example, the repair drone has both configuration values like `healingPerSecond` or `rayDefinition` (that's ok, could be better) and runtime values, like the `currentHealing` or the `rayEntity`. Having runtime values there is an issue for the future, and here are some of the reasons:

* It is not easy accessible from other controllers nor systems if need for other logic.
* For the same reason and also because data tends to not follow any common structure, it is harder to debug. When data is in Components since I can easily iterate in all the components and structure and data is normally more consistent.
* I can't reuse the same Controller instance, I need to have an instance per entity in that case, while for stateless Controllers I can make entities reuse the same instance. 
* It is harder to serialize in case I want save the state of an entity or send it over the network.

What I try to do now is, first, try to see if I can store the data I want in an existing Component that make sense. For example, if I am creating some cooldowns to execute some action, maybe I can canalize that with my Abilities system with a new Ability. If I can't, I just create a new Component, could be a "general" Component if it makes sense, or could be a specific Component. In the case of the Repair Drone, I could create a specific Component like this one: 

```csharp
public class RepairDroneComponent : IEntityComponent {
    public float currentHealing;
    public Entity rayEntity;
}
```

And then change the code to add that component in the Controller `Init()` and then use it from the entity, so each entity has their own. Or, in the hypothetical case I had a WeaponComponent previously defined maybe I could change that Controller to use that one (it is the closer concept I can think of right now).

## What about the configuration values?

-- TODO: talk about the configuration values, could be definition, component or even a separated configuration and that would be better too --  

# React to general events in Scripts (Controllers) 

# The Event Component

# TOOLS LIKE THE ECS DEBUGGER (EXTENDED VERSION OF THE LEOECS)

