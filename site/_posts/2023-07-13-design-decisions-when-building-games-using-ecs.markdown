---
layout: post
title:  "Design decisions when building games using ECS"
date:   2023-07-13 00:08:00 -0300
excerpt:  When using ECS (Entity Component System), how to decide which data goes in which Component and why?, which logic goes into which System? should I put logic in components?, and in the case of using a Scripting framework, which logic goes in scripts instead of systems?. This blog post tries to share how I approach these decisions after years of using different ECS solutions making games. 
author: Ariel Coppes
tags:
  - personal
  - update
image:
  path: /images/ecs-post-preview.jpg
  height: 100
  width: 100
---

{{page.excerpt}}

# Background

My experience with ECS goes back to 2010 when we started making games at Gemserk with [@rgarat](https://twitter.com/rgarat), we used [Artemis](http://entity-systems.wikidot.com/artemis-entity-system-framework) at that time for our games and even to make the port of Clash of the Olympians for mobile devices. Over the years I used different ECS solutions and even developed my own for [Iron Marines Invasion](https://www.ironmarinesinvasion.com/). For my current games I use the Community maintained fork of [LeoECS](https://github.com/LeoECSCommunity).

_Note: For IMI I first tried used Unity ECS but it wasn’t ready at that time._

I assume you already know about ECS but here is a great [source of information](https://github.com/SanderMertens/ecs-faq) as introduction. If you come from an OOP background you have to [change your mindset](https://github.com/SanderMertens/ecs-faq#how-is-ecs-different-from-oop) to maximize the value of using ECS. I also recommend this GDC talk about [Overwatch's ECS](https://youtu.be/zrIY0eIyqmI).

Even though Systems implement the logic, they depend directly on which components an entity has and the data inside them. This means the behaviour of the game when using ECS is mostly data driven: adding or removing a component and changing its data changes which systems execute and what they do. 

To understand the examples of this blog post, I first have to tell you that I use an scripting framework similar to the [one we created at Gemserk](https://blog.gemserk.com/2011/11/13/scripting-with-artemis/). Scripts have logic and data. Data can be readonly or mutable but the recommendation is to avoid the second by moving it into Components. Also, if no mutable data is used, Script instances can be reused between different entities.

**Where to put the data?** By definition, we know that it should be in a Component but, should it be in only one or distributed into multiple Components? Should I have data in systems? what about scripts? 

**Where to process the logic?** By definition, we know it should be in a System, but should it be in only one or distributed into multiple Systems? should it be in a Script or in multiple Scripts? should I put logic in Components even though I am not supposed to?

_Note: even though it is against the idea of ECS to have logic in components, some times it comes super handy to have helper logic in there in the form of properties, methods or even extension methods. Some times you are integrating an ECS to a previous codebase and some logic still runs in a class with the data (more on this later)_

The answer of all of these questions is that it obviously depends on the case but one thing I learned is that you might discover the best over time while it is normally easy to have a first step to start and change it later. In the next sections I will share some cases that might help you decide when facing this questions.

# Having too much data in one Component

There is a platformer game where the main character walks over platforms and can jump from platform to platform. For this game, we could have just a CharacterComponent with data like movement speed over platforms and air, jump speed and initial impulse when jumping, etc:

```csharp
struct CharacterComponent {
  float controlMovementDirection;
  bool controlJumpPressed;

  bool inContactWithPlatform;
  float platformSpeedOnGround;
  float platformSpeedOnAir;

  bool jumping;
  float jumpSpeed;
  float jumpInitialImpulse;
  // others related with the character
}
```

In this solution there might be a System processing the set CharacterComponent and in that iteration performing different game logic together.

A pseudo code system could be something like this:

```csharp
void Update() {
  foreach (e in set(CharacterComponent c)) {
    updateControls(c)
    updateJumping(c) // apply vertical movement with physics if jumping
    updatePlatformer(c); // detect if over platform or not and set in another component 
  }
}
```

That solution isn't wrong, those values need in some way to work together, you cant have a jump if you cant place the character (or whatever) over platforms but there are some code smells here:

* Variables with prefixes.
* System is doing lots of logic together and there might be logic acting on only part of the data. 
* I can't easily reuse only part of the logic and data. I might want to have something that can be over platforms and fall, but can't Jump, so I want to use only part of the data. I could always use a bool like `jumpEnabled` for example, to say the unit has no jump, but that means it is processed anyway by systems to compare and do nothing. It is better to not have that Component if not used.

_Note: These code smells are common in OOP too and one way to attack it is to separate in different classes with the data ana logic. In ECS a good practice is to extract a component and separate systems._

We could separate in different Components, for example:

```csharp
struct ControlComponent {
  float movementDirection;
  bool jumpPressed;
}

// Indicates something can be placed over platforms
struct PlatformerComponent {
  bool inContactWithPlatform;
  float speedOnGround;
  float speedOnAir;
}

// Indicats something can jump
struct JumpComponent {
  float speed;
  bool jumping;
  float initialImpulse; 
}
```
So now the system looks like this:

```csharp
foreach (e in set(ControlComponent c, JumpComponent j, PlatformerComponent p)) {
  updateControls(c, j) // configure jump component values from the controls
  updateControls(c, p) // configure platformer component values from the controls
  updateJumping(j); // update jumping logic 
  updatePlatformer(p); // update platformer logic
}
```

[//]: Note: in my case, working on an Endless Runner 2d game that uses Physics2d I reused a JumpComponent from the 2.5d Beat'em Up which was using Physics3d. The changes were in the systems, I added more systems or added some data in the component extending its reusability.

# Separated data is always processed together

It can also happen the opposite: having two Components that are always processed together and there is no way nor need to avoid it. If that happens, it is not bad but might be a code smell too since you have to create always iterations of that set of components and configure them apart, etc. A good practice for this case is to integrate all the data into one component.

# Having too much logic in one system

To continue with the previous example, even if you have a good data separation, you might have a system that does logic on lots of Components at the same time.

```csharp
foreach (e in set(ControlComponent c, JumpComponent j, PlatformerComponent p)) {
  updateControls(c, j) // configure jump component values from the controls
  updateControls(c, p) // configure platformer component values from the controls
  updateJumping(j); // update jumping logic 
  updatePlatformer(p); // update platformer logic
}
```

That works but it is not ideal. The first reason is, you need ALL of those components at the same time in order for the system to consider running the logic. So for an entity with only `[ControlComponent, JumpComponent]`, it will not execute. If you want that, the approach is to start separating into different systems.

```csharp
foreach (e in set(ControlComponent c, JumpComponent j)) {
  updateControls(c, j) // configure jump component values from the controls
}

foreach (e in set(ControlComponent c, PlatformerComponent p)) {
  updateControls(c, p) // configure platformer component values from the controls
}

foreach (e in set(JumpComponent j)) {
  updateJumping(j); // update jumping logic 
}

foreach (e in set(PlatformerComponent p)) { 
  updatePlatformer(p); // update platformer logic
}
```

We can now run part of the logic for entities with only some components and if going further, we could now parallelize logic in different threads/cores, using jobs, etc. 

_Note: when I talk about systems I mainly refer to the iteration over a set of components, not the class itself. I normally have one system class with more than one iteration, the drawback there is I can't reorder those iterations with other systems but the fix is creating a new system class and moving the iteration code there, and then reorder systems._

# Identify entities to include or exclude from logic: Tag Component

Empty components can be used as a way to identify entities for some logic. 

For example, you might have a set `[ComponentA, ComponentB]` and you want to run a logic over that set in a System only for a given condition. 

One approach could be to add a boolean to either the first or the second component and check for that during the iteration.  

Another approach is to add a tag component `ComponentProcessAB` to the entity and check for the complete set of components to iterate. Tagging an entity is equal to adding the component, and removing the tag is equal to remove the component.

The idea here is take advantage of the systems processing logic and iterate over the minimum set of entities. Most of the ECS solutions consider this and have some kind of cache to improve performance for this case but even if they don't, this approach is more ECS friendly and reduces data from components.

How to decide what approach to follow? in my case I normally use the first one since it is super easy and then I transition to the second (maybe after some time after the code is more stable). Sometimes I just know it is the right way and start with the second approach.

Tag Component can also be used to exclude entities, and don't execute logic if they have that component. For example, for my games I use a tag component named `DisabledComponent` to tell the system I don't want to execute logic for them. Since it is a core part of my engine I have to consider not having that component when processing entities in almost every system I have.

# One time logic that needs to run in systems: Command Component

There is another case of components that are used for only one frame, to mark a specific logic to be performed by a system. I named them Command Components since they act similar to a Command pattern.

Why using a command instead of just doing the logic as a helper method in the Component or as an extension? Well, sometimes the logic might be complex and do things only a system is aware of. Other times it needs runs at an specific order (after or before some system). For those cases I believe the Command Component makes sense.

It is a normal component with some data but the difference is that it is added when needed and removed after processed. 

For example a component likt this one, to switch skins:

```csharp
struct SpineSkinChangeCommand {
  string newSkin;
}
```


The logic to process this command runs after the spine model was created and initialized and before rendered. I also call this command from different places in code so it makes sense to encapsulate it.

# Systems delay of one or more frame for data

One great thing about ECS solutions is that they easily allow ordering logic, you can be sure some logic runs before or after another and this is really important to improve determinism and debug, among others.

However, there is also a issue when a system or a script want to react as soon as possible to some data change but it might need to wait to the next update. 

In my experience, it general (or for most of the systems) is not an big issue but for some others it is. For example, when firing a projectile maybe one system creates the entity and if the model loads in the next frame, the player might notice a visual delay after pressing the fire action. 

For this issue, a common problem is that the systems not ordered properly so in some cases they might need to wait one or more frames for some data to be in the final state, for example:

```csharp
public class World() {
  public void Update() {
    SystemC.update();
    SystemB.update();
    SystemA.update();
  }
}
```

Lets assume those systems depend on data in order, SystemA produces some data, SystemB updates it and finally SystemC does the last logic. In that world, that data is processed in 3 different frames. The fix for this is to reorder the systems.

```csharp
public class World() {
  public void Update() {
    SystemA.update();
    SystemB.update();
    SystemC.update();
  }
}
```

Deciding systems order might be easy at first, when having a low count of systems, but might become difficult over time. The good thing though is that not all systems depend on all the others so changing order of small chunks of systems normally work. However a good recommendation for that refactor is to have some tests.

Sometimes, can't fix it by just reordering. Lets go back to the projectile example, my approach to attack that problem consist in two things. 

The first part is to to react to entities creation as soon as possible. For that, my system API has callbacks for entity creation and destruction allowing systems to configure its first state. 

For the case of having to create heavy objects in order to work (for example a GameObject for the visuals of an entity) there are two approaches. One is to use pooling techniques to reduce creation costs (but still has activate/deactivate cost). The other one it so be lazy by delaying the heavy part creation to the last moment (for example, when it is about to be rendered).

The second part is that systems can execute in different Unity events (FixedUpdate, Update and LateUpdate), so some systems that update the visual model (position, rotation, etc) run during the LateUpdate before the entity is rendered. A pseudocode explaining the process (using the first approach for heavy objects):

```csharp
class SomeScript : Script {
  void Update(world, entity) {
    // some stuff
    var projectile = world.Create(projectileDefinition);
    // here it is already initialized but has might have no model
    projectile.damage = 100; 
  }
}

// fixed update
class ModelCreationSystem : System {
  void OnEntityCreated(Entity e) {
    if (e.has<ModelComponent>()) {
      // initialize model
      e.get<ModelComponent>().instance = Pool.get();
    }
  }
}

// late update
class ModelInstanceUpdateSystem : System {
  void Update() {
    foreach (e in set(ModelComponent m, Position p)) {
      m.instance.transform.position = p.value;
    }
  }
}
```

One thing I like about delaying the heavy stuff is that if I, for some reason decided to destroy the entity in the same frame it was created, the heavy objects were never created. 

### Story: State enter/exit events

In my engine there is the `StatesComponent` that holds a set of states an entity could be at a given time. For example, an entity could be "walking" and "stunned". This component has a basic API to set values, like `enterState(state)` or `leaveState(state)`. Initially I thought I needed to do the transition logic in systems so when I entered a state I didn't really enter the state, I stored a value like `enteredStates` to be processed later in a system.

This wasn't wrong but there was an issue, if I run the `enterState(state)`, I should be able to check if I am in that state in the next line of code and should be true but that wasn't happening, I had to wait one frame. After some hesitation I decided to change the code to enter/leave the state as soon as invoked the method but to hold state change data in order to be able to call scripts callbacks in the scripting system. 

The learning here is to not try to put everything in systems. Some logic, like adding two vectors for example, can be processed instantly and it is not wrong. 

# Integrating ECS to old OOP code base

When we started Iron Marines Invasion (IM2) we weren't completely sure if we should go full ECS, and we also had lots of code from first Iron Marines. So we decided to go step by step. 

During the development of IM1, we added a concept of formations for the second enemy race. It was similar to squads but allowed us to use it for enemies to follow a formation and even rotate it based on the leader movement direction. That concept of `Formation` was migrated to IM2 as core of the movement systems for all units. Since that class contained a lot of code that we wanted to reuse, and our new ECS was being developed, we integrated the code as it was, something like this:

```csharp
class Formation {
  Unit main;
  List<Unit> members;
  List<Vector2> positions;

  public void RecalculatePositions() {
    // ...
  } 

  // more methods
}
```

```csharp
struct SquadComponent {
  Formation formation;
  //...
}
```

```csharp
class SquadFormationSystem {
  void Update() {
    foreach (e in set(SquadComponent c)) {
      c.formation.RecalculatePositions();
    }
  }
}
```

To run that logic we just referenced added that class in some other components, like the SquadComponent for example, and then in systems we just called `squadComponent.formation.method()`, and that worked well. We had a mix between ECS and OOP. 

This was a good first step in a transition to ECS, you don't have to convert everything to ECS from the start. One issue, though, was that Formation instances were shared between units of the same formation, so we had a system factory method in order to create formations and we then set the instance to each entity, it was really not ECS friendly since we started having dependency to systems.

```csharp
class FormationsSystem {
  public Formation createFormation(parameters) {
    // 
  }
}
```

We had another similar case for the behaviours solution from IM1. Behaviours control a unit, one behaviour can run for some time and they are ordered in priority. As soon as a behaviour can run, it takes control of the unit until it completes it execution and releases control. To integrate this solution into IM2, we used the same approach and added a BehaviourComponent with the Behaviours class in there and then had a system where we just run `behaviourComponent.behaviours.Run(dt)`, and also worked fine.

_Note: this behavior solution was like a scripting framework too, it was the way to add custom/specific logic to elements of the game._ 

# Migrating from OOP to ECS

Continuing the previous story, for both cases we end up migrating to full ECS when we had clear how to, and when we started to need it in order to take advantage of the ECS.

For the behaviours solution, what we did was to move, step by step, the internal `Behaviour` logic to a System and its data to the Component. We ended up with something like this:

```csharp
struct Behaviour {
  bool executing;
  Script script;
}

struct BehavioursComponent {
  List<Behaviour> behaviours;
  Behaviour runningBehaviour;
  // more stuff
}

class BehavioursSystem {
  void Update() {

    // this logic (and more) was in the Behaviour concept from IM1
    foreach (e in set(BehavioursComponent b)) {
      foreach (behaviour in b.behaviours) {
        behaviour.RunPassive(); // logic that always run independently the behaviour is executing or not.
      }
      
      var executing = false;

      if (b.runningBehaviour) {
        executing = b.runningBehaviour.Run();
      }

      if (!executing) {
        // search for new behaviour to execute and make it the running behaviour.
      }
    }
  }
}
```

Making this change allowed us to start taking advantages of ECS. One of those was that we added a callbacks framework over the scripting and we could order when the callbacks are called, before or after `Run()`, and even order between them.

# Everything is an Entity

To complement the Formations story, when migrating the `Formation` concept we had that problem of sharing the Formation instance between units. To solve that we decided to make the Formation an entity itself, so any formation was an entity with a `FormationComponent`, and all units of that formation had a `FormationMemberComponent`. 

```csharp
struct FormationComponent {
  List<Entity> members;
  // form
  // positions
}

struct FormationMemberComponent {
  bool isLeader;
  Entity formation;
}
```

To use that, when an enemy squad was instantiated, a formation entity was instantiated with the first member (the leader of the formation) and both the leader and the formation we configured together. The formation had an identifier and then each time a new entity of the same squad was instantiated, we gather the formation from the world and configured together.

One good thing about having it as an Entity is that it allowed us to add Components to it in order to have more logic (or debug), and we even decided at some point that the formation and the squad were the same entity, and they were also the formation leader. When a formation was without units it was auto destroyed.

The learning here is that sometimes some lightweight, abstract or invisible (to the player) concepts might need to have a separated entity and from experience, it makes sense most of the time after you make the change and it opens new ECS possibilities. 

# New logic that needs to run in the middle of a system's logic

```csharp
public class System {
  public void Update() {
    foreach (e in set(ComponentA, ComponentB)) {
      // logic
    }
  }
}
```

We have a system with some logic, like the jumping logic which detects input and applies a force and we need to run new logic in the middle of that, before applying the force. One way to approach this is to make the original system more complex and add the new logic there, for example:

```csharp
public class System {
  public void Update() {
    foreach (e in set(ComponentA, ComponentB, ComponentC)) {
      // previous logic - first part
      // new logic
      // previous logic - second part
    }
  }
}
```

Even though it doesn't look bad, there is an issue. If you noticed, we now need to access the new data from ComponentC so now entities without that component are not being updated while they previously were. 

If we know all of those entities have all the components then it might not be an issue, at least not for now, but it normally becomes an issue at some point. 

Another option could've been to add more data to ComponentA and ComponentB but it is also an issue if for example is not related to those components.

To improve that we can separate in 3 systems, like this:

```csharp
foreach (e in set(ComponentA, ComponentB)) {
  // previous logic first part
}

foreach (e in set(ComponentA, ComponentB, ComponentC)) {
  // new logic
}

foreach (e in set(ComponentA, ComponentB)) {
  // previous logic second part
}
```

We might even discover we only need ComponentA and ComponentC for the new logic, so we can improve it further by removing ComponentB dependency. 

By separating we now can be sure the new logic runs in the middle and that also depends only on the minimum number of components in each logic.

_Tip: Try to depend in the minimum number of Components to make the code cleaner, easier to read and maintain but also to make it more reusable_

### Story: Having multiple Components of the same type in Iron Marines Invasion

Most of the ECS solutions don't allow having multiple Components of the same type but sometimes seems like a requirement. 

When I was developing our ECS for Iron Marines Invasion, I wasn't sure if I hade to support this feature or not. There were some special cases like the `AbilityComponent` which stored data about one ability but our units should be able to have multiple abilities. 

```csharp
struct AbilityComponent: IComponent {
  string name;
  float cooldown;
  bool isReady => cooldown <= 0;
  bool isRunning;
  // ...
}
```

Initially we supported having list of components of the same type, however, there was another solution. Change the component to be `AbilitiesComponent` and store a list of Ability (a new struct with the ability data), similar to storing a list of any data (a Vector2 for example). This is a better solution in my opinion since it has best of both worlds, there is no special case of having multiple components, components can be used as filters for queries and we still can have multiple data of one type. It also allow to have shared knowledge data in that Component.

```csharp
struct Ability {
  string name;
  float cooldown;
  bool isReady => cooldown <= 0;
  bool isRunning;
  // ... 
}

struct AbilitiesComponent: IComponent {
  List<Ability> abilities;
  Ability runningAbility;
}
```

Another example was having an actions (move, attack, build) queue for the units. Using the Command Component could've work but we still need a way to order them and support for repetition, so it wouldn't easily work in this case.

Since Actions normally share a common structure, for example:

```csharp
struct Action {
  string name;
  vector2 origin;
  vector2 target;
  // ... 
}
```

We decided to have a component `PendingActionsComponent` with the queue of actions. That queue was consumed in different systems and scripts that controlled the entity in order to do what the action needs. We used this in a generic way, like this:

```csharp
var p = e.Get<PendingActionsComponent>();
p.queue(new Action() {
  name = "moveTo",
  target = someTargetPosition
})
```

So that allowed also delegating what kind of actions we could perform to design by avoiding hardcoded commands in code. 

# My process when I don't know the best place for code or logic

For some logic it is super clear from the beginning where to code it, for others I am not so sure. For those, I normally start it as a script, even with mutable data (can't share the instance among entities).

```csharp
public MyNewScript : Script {
  public float someData;

  public void OnUpdate(world, entity) {
    if (somedata > 10) {
      entity.Get<Position>() += 5;
    }
  }
}
```

After that, I keep working on other things and if after some point some things could happen: 

* Maybe I don't use the script anymore and remove it (the best case btw).
* I need to read the data from other script or system.
* I want to reuse the logic for another entity and I don't want to add the ScriptComponent and all of that.

For the second, my process is to extract the data to a component so I now can use it elsewhere.

```csharp
struct MyNewComponent {
  float someData;
}

public MyNewScript : Script {
  public void OnUpdate(world, entity) {
    var newComponent = entity.Get<MyNewComponent>();

    if (newComponent.someData > 10) {
      entity.Get<Position>().value += 5;
    }

    // some other logic I am not interested in
  }
}
```

_Note: Now my script instance can be shared._

And for the third, I move the part of the interesting logic from the script into a system, almost directly. If it was all the logic, I remove the script also (best case).

```csharp
public MyNewSystem : System {
  public void Update() {
    foreach (e in set(MyNewComponent m, Position p)) {
      if (m.someData > 10) {
        p.value += 5;
      }
    }
  }
}
```

In my experience, each time I detected some logic could be reusable and spent time moving logic from scripts to systems at the end of the day it always felt the right thing to do. I also enjoy a lot removing unused code, and during the process I normally find some.

# Story: A system does super specific logic for only one element of the game  

In Iron Marines Invasion there is an enemy sniper tower that reveals itself over fog of war while it is targeting a player's unit. The idea here is to let the player react by relating the crosshair over the targeted unit (oh! someone is targeting my unit) with the tower targeting it (it is probably that tower that was revealed itself out of nowhere).

For that tower, we had a specific system that, if the main ability was executing and the entity has another specific ability (I don't remember exactly, it was kind of a mess), it added a new entity over the tower but on the main player team side with some vision, and as soon as the ability completes, the new entity is removed. The abstract solution was good but having that super specific logic in the system felt wrong since could've been in a Behaviour of the tower for that case.

Later in development we added a way of revealing enemies when firing (not targeting) if they are behind fog (that happens a lot when on higher ground), similar to what happens with siege tanks in Starcraft, and that completely made sense as a feature so we did it in systems and we separated the previous one to reuse the part of creating an entity to reveal fog.

_Note: This story is one of the reasons I normally start now by creating logic in Scripts and move it later to systems._

# Conclusions

To decide where to put the data and logic:

* If it is a super transversal feature, like walking, might be in a system.
* If it is super specific feature like one entity doing something in specific level, then use a script. It can always be [converted](#my-process-when-i-dont-know-the-best-place-for-code-or-logic) later.
* If it is not clear, it is normally easier to start it as a script. It can always be [converted](#my-process-when-i-dont-know-the-best-place-for-code-or-logic) later.

Some times for a game a feature is broader than for other games, but if you make it a clean component + system then it is always better to reuse it or not use it at all.

ECS and OOP can live together, this normally happens when transitioning from old codebase to ECS.

Having logic in Components or in helper methods is not bad, at least not when it is simple and tend to be readonly.

By working using the ECS paradigm, there are also code smells and best practices start to arise, and both are related to the ones that happen in OOP but they just require different solutions. For example, instead of extracting class with data and logic, in ECS you have to move data to another component, and logic to another system/iteration. 

It is better to have more systems running small chunks of logic and execute over a reduced number of components.

My general advice tough is to try to make the solution as refactorable as possible in order to support improving it over time, step by step. That can be done by exercising code refactor (and in that process, identify places to make refactoring easier), using a good IDE like Rider and making tools and tests to help you. 

Speaking about tests, I have a pending blogpost about testing in games that I have mind for a while now, hope to have some time to write it soon.

I probably have so much more cases and examples to share, I might come back to this topic later.

Hope you liked it, remember to share on whatever social network you use. 

Thanks for reading!!

_BTW, it seems you can buy me a coffee if you want now, much appreciated._