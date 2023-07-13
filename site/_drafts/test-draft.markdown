---
layout: post
title:  "Design decisions when building games using ECS"
# date:   2022-11-22 00:08:30 -0300
excerpt:  When using ECS (Entity Component System), how to decide which data goes in which Component and why?, which logic goes into which System? should I put logic in components?, and in the case of using a Scripting framework, which logic goes in scripts instead of systems?. This blog post tries to share how I approach these decisions after years of using different ECS solutions making games. 
author: Ariel Coppes
tags:
  - personal
  - update
---

{{page.excerpt}}

# Background

My experience with ECS goes back to 2010 when we started making games at Gemserk with [@rgarat](https://twitter.com/rgarat), we used [Artemis](http://entity-systems.wikidot.com/artemis-entity-system-framework) at that time for our games and even to make the port of Clash of the Olympians for mobile devices. Over the years I used different ECS solutions and even developed my own for [Iron Marines Invasion](https://www.ironmarinesinvasion.com/)[^1]. For my current games I use the Community maintained fork of [LeoECS](https://github.com/LeoECSCommunity).

I assume you already know about ECS but here is a great [source of information](https://github.com/SanderMertens/ecs-faq) as introduction. If you come from an OOP background you have to [change your mindset](https://github.com/SanderMertens/ecs-faq#how-is-ecs-different-from-oop) to maximize the value of using ECS. I also recommend this GDC talk about [Overwatch's ECS](https://youtu.be/zrIY0eIyqmI).

Even though Systems implement the logic, they depend directly on which components an entity has and the data inside them. This means the behaviour of the game when using ECS is mostly data driven: adding or removing a component and changing its data changes which systems execute and what they do. 

To understand the examples of this blog post, I first have to tell you that I use an scripting framework similar to the [one we created at Gemserk](https://blog.gemserk.com/2011/11/13/scripting-with-artemis/)[^2]. Scripts have logic and data. Data can be readonly or mutable but the recommendation is to avoid the second by moving it into Components. 

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

One great thing about ECS is that easily allow ordering logic, you can be sure some logic runs before or after another and this is really important to improve determinism, debug, etc.

However, there is also a drawback when a system or a script want to react as soon as possible to some data change but it might need to wait to the next update. 

In my experience, it general (for most of the systems) is not an big issue but for some others it is, mainly when related with things the player can see. For example, when firing a projectile maybe one system creates the projectile entity and if the model loads in the next frame, the player might notice a visual delay. 

For this issue, some times the main problem is that the systems not ordered properly so in some cases they might need to wait even one or more frames for some data to be in the final state, for example:

```csharp
public class World() {
  public void Update() {
    SystemC.update();
    SystemB.update();
    SystemA.update();
  }
}

```

Lets assume those systems depend on data in order, SystemA produces some data, SystemB updates it and finally SystemC does the last logic. In that world, that data is processed in 3 different frames. The fix is to reorder systems.

Reordering might be easy at first when having low count of systems but might become difficult over time. The good thing though is not all systems depend on all systems so changing order of small chunks of systems normally don't break anything. However, having good tests here simplify reordering changes.

Now, back to the projectile example, my approach consist in two things. 

The first one is to to react to entities creation as soon as possible. For that my system API has callbacks for entity creation and destruction. That allows systems to preconfigure as much as possible. This could be an issue if a systems needs to create heavy objects in order to work (for example a gameobject for the visuals of an entity). The approach here is to be lazy delaying the heavy part to the last moment.

The second part is that systems can execute in different Unity events (FixedUpdate, Update and LateUpdate), so for model creation (heavy objects) and update (positioning) I delay it as much as possible (LateUpdate). At that time I am sure all the important systems from FixedUpdate already run and I know I am about the entity is about to be rendered. 

One thing I like about delaying the heavy stuff is that if I, for some reason decided to destroy the entity in the same frame it was created, the render objects are never created. 

### Story: State enter/exit events

In my engine there is the `StatesComponent` that holds a set of states an entity could be at a given time. For example, an entity could be "walking" and "stunned". This component has a basic API to set values, like `enterState(state)` or `leaveState(state)`. Initially I thought I needed to do all logic in systems so when I entered a state I didn't really enter the state, I stored a value like `enteredStates` to be processed later in a system.

This wasn't wrong but there was an issue, if I run the `enterState(state)`, I should be able to check if I am in that state in the next line of code and should be true but what was happening was I had to wait one frame. After some hesitation I decided to change the code to enter/leave the state as soon as invoked the method but to hold data in the component so the system can compare and call corresponding callbacks to the scripting framework. 

The learning here is to not try to put everything in systems, some logic that can be processed directly (like adding two vectors for example) to avoid systems complexity. 

# Integrating ECS to old OOP code base

When we started Iron Marines Invasion (IM2) we weren't completely sure if we should go full ECS, and we also had lots of code from Iron Marines, so we decided to go step by step. When making IM1, we added a concept of formations for the second enemy race, it was similar to squads but allowed us to use it for enemies to follow a form and even rotate in a direction. That concept of `Formation` was migrated to IM2 as core of the movement systems for squads and enemies. Since that class contained a lot of code that we wanted to reuse, and as a team we had little experience with our new ECS, we integrated as it was. It was something like this:

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

To run that logic we just referenced added that class in some other components, like the SquadComponent for example, and then in systems we just called `squadComponent.formation.method()`, and that worked well. We had a mix between ECS and OOP. This was a good first step in a transition to ECS, you don't have to convert everything to ECS from the start. One issue though was that Formation instances were shared between units of the same formation, so we had a system factory method in order to create formations and we then set the instance to each entity, it was really not ECS friendly.

We had another similar case for the behaviours of IM1. Behaviours control a unit, one behaviour can run at a time and they are ordered in priority, as soon as a behaviour can run, it takes control of the unit until it completes and release control. To integrate in IM2, we used the same concept and added a BehaviourComponent with the Behaviours class in there and then had a system where we just run `behaviourComponent.behaviours.Run(dt)`, and also worked fine. 

# Migrating from OOP to ECS

Continuing the previous story, for both cases we end up migrating to full ECS when we had clear how to, and when we started to need it in order to take advantage of the ECS.

* TODO: explain how we migrated the behaviours class and what we could achieve after that change

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

To use that, when an enemy squad was instantiated, with the first member (the leader of the formation) the formation entity was instantiated too and both the leader and the formation we configured together. The formation had an identifier and then each time a new entity of the same squad was instantiated, we gather the formation from the world and configured together.

One good thing about having it as an Entity is that it allowed us to make add Components to it in order to have more logic, and we even decided at some point that the formation and the squad could be the same entity.

My take here is that sometimes some lightweight or abstract concepts might need a separated entity, makes sense most of the time after you make the change and it opens tons of new possibilities. 

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

# My process when I don't know the best place for code or logic (TODO)

// TODO: explain this more in detail and maybe with examples.

* I start with scritps both data and logic, then I want to read the data in somewhere else, so I move the data to components, then I want to reuse the logic, I move part of the logic to systems.

In my experience, each time I detected some logic could be reusable and spent time moving logic from scripts to systems at the end of the day it always felt the right thing to do.

# Examples (TODO or REMOVE)

* In IMI, show in fog while targeting (vultures sniper tower) is a super specific system only used by that tower

# Conclusions (WIP)

My tips to decide where to put the data and logic:

* If it is a super transversal feature, like walking, might be in a system.
* If it is super specific feature like one entity doing something in specific level, then logic could be in a script and the data in a blackboard.
* If you don't know it is normally easier to start as script and scale it to a system, move data first to a component and then move the (or parts of the) logic to one or more systems
* some times for a game a feature is broader than for other games, but if you make it a clean component+system then it is always better.

My most important advice for you is to try to make your solution as refactorable as possible in order to support improving it over time, step by step. How? well, first by exercising code refactor (and in that process, identify places to make refactoring easier), using a good IDE like Rider and making tools to help you. 

By working using the ECS paradigm, there are also code smells and best practices start to arise, and both are related to the ones that happen in OOP but they just require different solutions.
 
* Separate data in different Components to achieve clarity, reusability, separation of concerns.
* Separate logic in different systems, ir order to reduce coupling, improve parallelism and make the code more modular and also to control logic order.

One great thing about ECS is that easily allow ordering logic, you can be sure some logic runs before or after another. However, there is also an issue with this when a system or a script want to react as soon as possible to some data change but it might need to wait to the next update. In my experience, it is normally not an issue when it is internal logic but it is common when you render stuff visually.

It is better to have more systems and run small chunks of logic over reduced number of components.

Notes

[^1]: I first tried used Unity ECS but it wasn't ready at that time.

[^2]: We created our own [scripting system](https://blog.gemserk.com/2011/11/13/scripting-with-artemis/) since it felt like obviously necessary to have logic that runs outside systems.