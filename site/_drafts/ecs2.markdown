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

As I mentioned before, I am developing Ship Miner, and my other prototypes, following the ECS approach. I decided to use [leoecs-lite](https://github.com/LeoECSCommunity/ecslite) as the ECS solution and I built my own systems and tools over it. 

Here is a video of Ship Miner and the link to add to your steam wishlist.

<div class="post-image">
<video controls width="100%" autoplay="autoplay" muted="muted">
  <source src="/assets/shipminer/shipminer-demo-announcement-rc1.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video> 
</div>

<div align="center">
<iframe src="https://store.steampowered.com/widget/4028800/?utm_source=personalpage&utm_campaign=announcement" frameborder="0" width="646" height="190"></iframe>
</div>

# Introduction

One important feature is to have a scripting framework to be able to run specific logic for some entities. In my solution the scripts are named `Controllers` and they are stored in a `ControllersComponent`. Compared to do all logic in systems, using a scripting framework is friendlier with optional logic that could be even added/removed dynamically and with modding support too. 

For example, the RepairDrone Controller looks something like this: 

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

<div class="post-image">
  <img src="/assets/ecs2/repairdrone-controller-inspector.png" />
  <span>This is how it looks to configure that controller in the Unity inspector</span>
</div>

This is super specific to this unit, it checks for targets to repair inside some range, if it finds one, it locks on a target and decides to move there, once it is close it activates a ray to repair the target, and then disables the ray for some time. In runtime looks something like this:

<div style="text-align:center">
<video width="480" height="270" controls>
  <source src="/assets/ecs2/repairdrone-findtargets.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
</div>

The role of the Controller is to be the unit's brain manipulating the data stored in components to make the systems react to those changes. 

# Controllers System Sneak Peak

I don't want to go into too much detail of the code but basically I have a `ControllersComponent` in the entities, in the definition I point to a prefab where all the Controllers are stored. For example, the Repair Drone has the following prefab definition:

<div class="post-image">
  <img src="/assets/ecs2/repairdrone-definition-controllers.png" />
  <span>This is how part of the definition (entity prefab) looks.</span>
</div>

This entity definition in particular uses two controllers, the `BaseDroneController` with some common logic for drones (note: could be moved to systems) and the `RepairDroneController` with the main logic.

When a new entity with the `ControllersComponent` is created, the `ControllerSystem` initializes it by getting all the Controllers declared in the prefab and registering them in the component. Later, the `ControllerUpdateSystem` iterates over the controllers in the component and call the `Update` method for Controllers implementing that callback (oversimplified view, for more real code, check the [github](https://github.com/acoppes/unity-gemserk-utilities)).

# Using Stateless Controllers

In my previous post I talked about my process when [I didn't know where I should put new logic](/2023/07/13/design-decisions-when-building-games-using-ecs.html#my-process-when-i-dont-know-the-best-place-for-code-or-logic) if it should be a system or a controller. Now, after using that workflow multiple times, another thing that I learned is that, when I decide to go with a Controller, I should always try to keep them as stateless as possible, and it is usually simple to do it.

In the previous example, the repair drone has both configuration values like `healingPerSecond` or `rayDefinition` (that's ok, could be better) and runtime values, like the `currentHealing` or the `rayEntity`. Having runtime values there is an issue for the future, and here are some of the reasons:

* It is not easy accessible from other controllers nor systems if needed for other logic.
* For the same reason and also because data in controllers tends to not follow any common structure, it is harder to debug. When data is in Components it is easier because it normally follows a more consistent structure and also it is easy to iterate over components.
* I can't reuse the same Controller instance, I need to have an instance per entity in that case, while for stateless Controllers I can make entities reuse the same instance. 
* It is harder to serialize in case I want save the state of an entity or send it over the network.

What I try to do now is, first, try to see if I can store the data I want in an existing Component that make sense. For example, if I am creating some cooldowns to execute some action, maybe I can channel that using other Components I already have, like the [AbilitiesComponent](https://github.com/acoppes/unity-gemserk-utilities/blob/main/Packages/com.gemserk.gamescore/Components/Abilities/AbilitiesComponent.cs). If I can't, I just create a new Component, could be a general use Component if it makes sense, or could be a specific Component. In the case of the Repair Drone, I could create a specific Component like this one: 

```csharp
public class RepairDroneComponent : IEntityComponent {
    public float currentHealing;
    public Entity rayEntity;
}
```

And then either add that Component as part of that Entity Definition or add that Component dynamically in the Controller `Init()` and then use it by accessing the `entity.Get<RepairDroneComponent>()` inside the Controller's logic.

## And what about the configuration values?

Well, having configuration values it is a bit less troublesome since normally the values are kinda constant but still have some issues:

* If I want different configuration values, I need for sure different Controller prefabs for each config, so I can't reuse the Controller instance, but it is less bad since I can reuse per entity type normally, and it is also normal to have different list of controllers in different entity definitions.
* It makes less clear where the entity definition configuration is, sometimes is in Components, sometimes in the Controller, and I have to explore multiple times.
* It is also harder to have it visible in debug, I have to go to each definition to see the configuration values.

To improve this, I can either move those values to a new Component, for example RepairDroneConfigurationComponent or could even be the same Component I use for the runtime data if I want to. Another option could be to move it to a separated configuration concept (we used that in Iron Marines 2).  

## Configuration System

When we worked did our ECS during the development of Iron Marines 2, we knew we wanted to have configurations like we had in IM1, with a json file where game designers could easily modify values and/or customize units.

The solution worked like this, there was a `ConfigurationComponent` declaring the configuration key to use. For example, the ranger unit could have a `army.units.unit_ranger` as the configuration key.

Then, there were multiple json files with a structure like this for example: 

```json
{
    "army" : {
        "units" : {
            "unit_ranger" : { 
                "health" : 100,
                "abilities" : {
                    "base_attack" : {
                        "damage" : 100
                    }
                }
            }
        }
    }
}
```

Then, there was a `ConfigurationSystem` that pre processed the json files to create a dictionary of values, and then there were multiple subsystems that configured each component of the entity, for example: 

```csharp
foreach (e in <ConfigurationComponent, HealthComponent>) {
    if (e.configuration.contains("health")) {
        e.health.total = e.configuration.asFloat("health")
    }
}

// ...

foreach (e in <ConfigurationComponent, AbilitiesComponent>) {
    foreach (ability in e.abilities) {
        if (e.configuration.contains($"abilities.{ability.name}")) {
            ability.damage  = e.configuration.asFloat($"abilities.{ability.name}.damage")
        }
    }
}
```
_NOTE: as always, I am oversimplifying the code but the configuration supported accessing the keys with a flattened version like the second subsystem example._ 

This allowed us to expose to game designers only the things they needed to configure and balance the game in a generic ecs way. One great lesson was that by systemizing we needed to create a common structure to configure similar things and that improved a lot the trial and error compared to IM1 where we decided how to configure each unit each time and it was more difficult to use same criteria for everything and game designers were some times trying stuff it didn't exist. For example, for one hero I developed I decide the health would be named `life` and then another developer decided for another hero it should be named `hitpoints`, and since we loaded the configuration in each unit logic, that was possible. 

We still supported special cases, if there was a configuration that made no sense to be a general one, the Controller of the unit could access the `ConfigurationComponent` and try to get the specific value. If at some point we decided that config should be more general, we could create a new component and then add a new configuration subsystem to read from the config to that component, and remove the specific code from the unit. That process is related to the to the I don't know where to put logic if controllers or systems.

# Reacting to events in Controllers

In order to react to events in Controllers what I do is to have a way to implement different callbacks and then I have specific systems to call them if the event was triggered. As an example, there is a `IHealthStateChanged` interface to implement to be called when the unit either died or came back to life. After the `HealthSystem` is processed there is another system, the `OnHealthStateChangedControllerSystem`, that checks for the health state change and if that happened, it iterates over all the controllers implementing the callback and calling them. It is optional but it helps me to react to events in the specific unit logic.  

This could be the reaction system code:
```csharp
public class OnHealthStateChangedControllerSystem: System {
    public void Update() {
        foreach (e in filter<HealthComponent, ControllersComponent>()) {
            if (e.health.previousState != e.health.currentState) {
                foreach (controller in e.controllers) {
                    if (controller implements IHealthStateEvent) {
                        controller.OnHealthStateChanged(e)
                    }
                }
            }
        }
    }
}
```

# Event Components

One thing I use in some cases is to add a temporary component, the `Event` components, to react from outside to some important data change that happened in a system. It is kinda the opposite to the `Command` component [I explained in the previous post](/2023/07/13/design-decisions-when-building-games-using-ecs.html#one-time-logic-that-needs-to-run-in-systems-command-component) which was also temporary and used from outside to make systems react with some data change. 

In the case of the `HealthSystem` what I normally do is to store two values in the `HealthComponent`, the previous and the current value, so I can check that in the other systems to decide to run or not. But in some cases, I prefer to not have that exposed, and to add a new temporary event Component, maybe with more information, and then remove it in the next iteration. This helps in not having to have all that extra information in the base component and even to not have to track the previous state.

For example, if I wanted to store how the entity died and where it was when that happened, I could create a `HealthStateEventComponent` like this:

```csharp
public struct HealthStateEventComponent : IEventComponent {
    public Vector3 position;
    public Vector3 damagePosition;
    public bool died;
    public string extraInformation;
}
```

Then, the `HealthSystem` would change part of its code to something like this: 

```csharp
public class HealthSystem: System{
    public void Update() {
        
        foreach (e in filter<HealthStateEventComponent>()) {
            world.Remove(e, HealthStateEventComponent);
        }

        foreach (e in filter<HealthComponent>()) {
            var previousState = e.health.current;

            // -- process damage logic here and store killDamage to use late -- 

            if (previousState != e.health.current) {
                world.add(e, new HealthStateEventComponent() {
                    died = e.health.state == death
                    damagePosition = killDamage.position
                })
            }
        }

        foreach (e in filter<HealthStateEventComponent, PositionComponent>()) {
            e.healthStateEvent.position = e.position;
        }
    }
}
```

And the `OnHealthStateChangedControllerSystem` would change to iterate only on the entities with that Component and avoid the health changed state check internally. For example, that system could be changed to this:

```csharp
public class OnHealthStateChangedControllerSystem: System{
    public void Update() {
        foreach (e in filter<HealthStateEventComponent, ControllersComponent>()) {
            foreach (controller in e.controllers) {
                if (controller implements IHealthStateEvent) {
                    controller.OnHealthStateChanged(e)
                }
            }
        }
    }
}
```

Suppose now I want to react to that new event in the RepairDrone to do some last blast when it dies, I could do something like this:

```csharp
public class RepairDroneController : ControllerBase, IHealthStateEvent
{
    // ... OTHER CODE ...

    public void OnHealthStateChanged(Entity e) {
        var eventData = e.Get<HealthStateEventComponent>()

        if (eventData.died) {
            var blastEntity = world.CreateEntity(blastDefinition);
            blastEntity.Get<Position>().value = eventData.damagePosition;
        }
    }
}
```

# TOOLS LIKE THE ECS DEBUGGER (EXTENDED VERSION OF THE LEOECS)

-- TODO: talk about dead pr and fork? -- 