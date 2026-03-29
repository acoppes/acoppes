---
layout: post
title:  "Design decisions when building games using ECS II"
# date:   2026-29-03 00:17:00 -0300
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

Even though the [previous part](/2023/07/13/design-decisions-when-building-games-using-ecs.html) covered a lot of aspects on how I am using ECS (entity component system) when making games, I have a couple of new examples and stories to share.

For the ECS solution I decided to use [leoecs-lite](https://github.com/LeoECSCommunity/ecslite), with my own systems and tools over it, and I used this solution for my game jams and also for my current in development game Ship Miner:

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

When making games, one important feature to have is a scripting framework to support running specific logic in some entities. In my solution the scripts are named `Controllers` and they are stored in a `ControllersComponent`. Using a scripting framework is better for logic that can be added and removed dynamically (I suppose it also helps when adding mod support). 

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

Since it is a MonoBehaviour, it can be configured in the Unity inspector like this:

<div class="post-image">
  <img src="/assets/ecs2/repairdrone-controller-inspector.png" />
</div>

This is super specific to this unit, it checks for targets to repair inside some range, if it finds one, it locks on a target and decides to move there, once it is close it activates a ray to repair the target, and then disables the ray for some time. In the game looks like this:

<div style="text-align:center">
<video width="480" height="270" controls>
  <source src="/assets/ecs2/repairdrone-findtargets.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
</div>

The role of the Controller is to be the unit's brain manipulating the data stored in components to make the systems react to those changes. 

# Controllers System

I don't want to go into too much detail of the code but basically I have a `ControllersComponent` in the entities, in the definition I point to a prefab where all the Controllers are stored. For example, the Repair Drone has the following prefab definition:

<div class="post-image">
  <img src="/assets/ecs2/repairdrone-definition-controllers.png" />
  <span>This is how part of the definition (entity prefab) looks.</span>
</div>

This entity definition in particular uses two controllers, the `BaseDroneController` with some common logic for drones (note: could be moved to systems) and the `RepairDroneController` with the main logic.

When a new entity with the `ControllersComponent` is created, the `ControllerSystem` initializes it by getting all the Controllers declared in the prefab and registering them in the component. Later, the `ControllerUpdateSystem` iterates over the controllers in the component and call the `Update` method for Controllers implementing that callback. Is is a simplified view of the system, for more information check the [code at github](https://github.com/acoppes/unity-gemserk-utilities).

# Using Stateless Controllers

In my previous post I talked about my process when [I didn't know where I should put new logic](/2023/07/13/design-decisions-when-building-games-using-ecs.html#my-process-when-i-dont-know-the-best-place-for-code-or-logic) if it should be a system or a controller. Now, after using that workflow multiple times, another thing that I learned is that, when I decide to go with a Controller, I should always try to keep them as stateless as possible, and it is usually simple to do it.

In the previous example, the repair drone has both configuration values like `healingPerSecond` or `rayDefinition` (that's ok, could be better) and runtime values, like the `currentHealing` or the `rayEntity`. Having runtime values there is an issue for the future, and here are some of the reasons:

* It is not easy accessible from other controllers nor systems if needed for other logic.
* For the same reason and also because data in controllers tends to not follow any common structure, it is harder to debug. When data is in Components it is easier because it normally follows a more consistent structure and also it is easy to iterate over components.
* I can't reuse the same Controller instance, I need to have an instance per entity in that case, while for stateless Controllers I can make entities reuse the same instance (and it also simplifies pooling). 
* It is harder to serialize in case I want save/load the state of an entity or send it over the network.

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

* If I want different configuration values, I need for sure different Controller prefabs for each config, so I can't reuse the Controller instance in that case but I can reuse it per unit type, and it is also normal to have different list of controllers in different entity definitions. For example, the Scout Drone entity definition also uses the `BaseDroneController`.
* It makes less clear where the entity definition configuration is, sometimes is in Components, sometimes in the Controller, and sometimes I lose time exploring where the data is.
* It is also harder to have it visible in debug, I have to go to each definition prefab to see the configuration values.
* I can't touch the configuration values in runtime, otherwise they become runtime values now.

To improve this, I can either move those values to a new Component, for example RepairDroneConfigurationComponent or could even be the same Component I use for the runtime data if I want to. 

Another option could be to move it to use a separated configuration concept, like we did in Iron Marines 2, and that's the next story.  

## How we did configurations in Iron Marines 2

When we did our ECS during the development of Iron Marines 2, we knew that we wanted to have configurations like we had in IM1, using json files where game designers could easily modify values and/or customize units.

The solution worked like this, there was a `ConfigurationComponent` declaring the configuration key to use. For example, the ranger unit used the `army.units.unit_ranger` as the configuration key.

Then, there were json files for configurations with a structure similar to this: 

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

The `ConfigurationComponent` stored the key and a cached dictionary for everything that was inside that key, for example, in the case of the previous key, the dictionary will only contain the values for `unit_ranger`.

The `ConfigurationSystem` pre processes the json files and it created the cached dictionary of values and stored it in the `ConfigurationComponent`. After that, there were multiple subsystems that configured each component of the entity, for example: 

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

This allowed us to expose to game designers only the things they needed to configure and balance the game. One great lesson was that by systemizing we needed to create a common structure to configure similar things and that improved a lot the trial and error compared to IM1 where we decided how to configure each unit each time and it was more difficult to use same criteria for everything and game designers were some times trying stuff that didn't exist. For example, for one hero I might decide the health would be named `life` in the configuration and then another developer might decide it should be named `hitpoints` for another hero he is developing, and since we loaded the configuration in each unit logic, that was more common. 

We still supported special cases, if there was a configuration that made no sense to be a general one, the Controller of the unit could access the `ConfigurationComponent` and try to get the specific value. If at some point we decided that config should be more general, we could create a new component and then add a new configuration subsystem to read from the config to that component, and remove the specific code from the unit. That process is related to the to the I don't know where to put logic if controllers or systems.

In case of modifying the configuration, there was a way of asking the `ConfigurationSystem` to reload the values. 

One interesting point of this configuration was supporting overrides of entries in the main dictionary, so if we wanted to have special values for some unit either by development scenes or because there was an upgrade active, what we did was just override the main dictionary values and then the unit was already reading from that, which leads me to the `UpgradesSystem` story.

## How we did upgrades in Iron Marines 2

I already spoiled this a bit but the main idea was to override values in the main configurations dictionary. What we did for that was to have jsons with specific values to override, for example:

```json
{
    "upgrade_super_rangers" : {
        "army.units.unit_ranger" : {
            "health" : [200, 300, 400, 500]
        }
    }
}
```

We treated overrides as arrays and we considered the level of the upgrade to read from there when overriding. In that example, the first level for that upgrade will override the `health` with the number `200` so when the configuration system configures the health, it uses the new value.

I remember also adding some extra support to declare stuff as percentage or factors, so instead of doing the final number we could do something like this: 

```json
{
    "upgrade_super_rangers" : {
        "army.units.unit_ranger" : {
            "healthPercentage" : [120, 140, 160, 200]
        }
    }
}
```

In that example instead of overriding the health, the final health was the original value * the specified percentage, so for the last level of the upgrade the health would be `200`, considering the value specified in the configuration.

# Reacting to events in Controllers

In order to react to events in Controllers what I do is to have a way to implement different callbacks and then I have specific systems to call them if the event was triggered. As an example, there is a `IHealthStateChanged` interface to implement to be called when the unit either died or came back to life. After the `HealthSystem` is processed there is another system, the `OnHealthStateChangedControllerSystem`, that checks for the health state change and if that happened, it iterates over all the controllers implementing the callback and invoke them. It is optional but it helps me to react to events in the scripting system.  

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

One thing I use in some cases is to add a temporary component, the `Event` components, to react to some important data change that happened in a system. It is kinda the opposite to the `Command` component [I explained in the previous post](/2023/07/13/design-decisions-when-building-games-using-ecs.html#one-time-logic-that-needs-to-run-in-systems-command-component) which was also temporary component and it is used to trigger systems to do some data change.

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

            // process damage logic here and store killDamage to use later 

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

# Custom Editor Window for Entities

LeoECS lite already has a way to see components data in the inspector but it does it with gameobjects and over time it became a bit limited for me so I decided to create my own editor window reusing part of the code.

This is the window I normally use when checking for data:

<div class="post-image">
  <img src="/assets/ecs2/ecs-window-showcomponent.png" />
</div>

It allows me to do query filter like systems do, for example, I can check for entities containing some components but not others, and then I can also filter by component names, sort by name, etc. 

<div class="post-image">
  <img src="/assets/ecs2/ecs-window-withfilters.png" />
  <span>In this case I am filtering entities with GameObjectComponent and without ConfigurationComponent</span>
</div>

For entities with `NameComponent`, I also show the name together with the entity id to easily identify them (for example the player ship).

By default, it supports all type of components with plain basic fields, but when components have more data like lists for example, it doesn't show that. However the default debugger supports implementing some custom inspectors in a generic way to show those cases as you want, but you need to implement that. For example, this is the custom inspector for MineralsCollectorComponent:

```csharp
sealed class MineralsCollectorInspector : EcsComponentInspectorTyped<MineralCollectorComponent>
{
    public override bool OnGuiTyped(string label, ref MineralCollectorComponent collector,
        EcsEntityDebugView entityView)
    {
        EditorGUI.indentLevel++;

        for (var i = 0; i < collector.minerals.Length; i++)
        {
            var mineral = collector.minerals[i];
            var mineralName = MineralTypes.ValueToName(i);
            
            if (mineral > 0 && !string.IsNullOrEmpty(mineralName))
            {
                EditorGUI.BeginChangeCheck();
                mineral = EditorGUILayout.FloatField(mineralName, mineral);
                if (EditorGUI.EndChangeCheck())
                {
                    collector.minerals[i] = mineral;
                }
            }
        }

        EditorGUI.indentLevel--;

        return true;
    }
}
``` 

That is pretty handy since it is autodetected by the ecs custom editor. In there I could also modify data and/or show it in a inspector way.

# Conclusion

Now that I am at the end of the article, I remembered a couple of extra cases to talk about but will leave that for a third part of the series, I hope to write that one soon not like this one that was two years later xD. A couple of topics to leave in mind for me are the `GameObjectComponent` used to connect the entity with a GameObject and its `EntityReference` corresponding MonoBehaviour, and which data I should try to avoid in Components to make them easier to serialize.

Will leave this one here for now.

As always I hope it was useful and please ask me in social networks or join ship miner discord or whatever you want in case you want to interact for more information.

Thanks for reading!!

Add Ship Miner to your Steam Wishlist!!

<div align="center">
<iframe src="https://store.steampowered.com/widget/4028800/?utm_source=personalpage&utm_campaign=announcement" frameborder="0" width="646" height="190"></iframe>
</div>

See you soon! 