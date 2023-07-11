---
layout: post
title:  "Design decisions when building games using ECS"
# date:   2022-11-22 00:08:30 -0300
excerpt:  When using ECS (Entity Component System), how to decide which data goes in which Component and why?, which logic goes in which System?, and in the case of using a Scripting framework, which logic goes in scripts instead of systems?. This blog post tries to share how I approach these decisions after years of using different ECS solutions making different games. 
author: Ariel Coppes
tags:
  - personal
  - update
---

{{page.excerpt}}

# Background

My experience with ECS goes back to 2010 when we started making games at Gemserk with [@rgarat](https://twitter.com/rgarat), we used [Artemis](http://entity-systems.wikidot.com/artemis-entity-system-framework) at that time and we used it to make the port of Clash of the Olympians for mobile devices. Over the years I used different ECS solutions and I even developed my own for [Iron Marines Invasion](https://www.ironmarinesinvasion.com/)[^1]. Nowadays, I use the Community maintained fork of [LeoECS](https://github.com/LeoECSCommunity) for my games.

I assume you already know about ECS but here is a great [source of information](https://github.com/SanderMertens/ecs-faq) as introduction. If you come from an OOP background you have to [change your mindset](https://github.com/SanderMertens/ecs-faq#how-is-ecs-different-from-oop) to maximize the value of using ECS. I also recommend this GDC talk about [Overwatch's ECS](https://youtu.be/zrIY0eIyqmI?t=102).

Even though Systems implement the logic, they depend directly on which components the entity has and their data. This means the behaviour of the application in ECS is mostly data driven: adding or removing a component or changing its data will change which systems execute and what they do. 

To understand the examples of this blog post, I first have to tell you that I use a scripting framework similar to the [one we created at Gemserk](https://blog.gemserk.com/2011/11/13/scripting-with-artemis/)[^2]. Scripts have logic and readonly and mutable data but the second is discouraged by moving that data into Components. 

**Where to put the data?** By definition, we know that it should be in a Component but, should it be in only one or distributed into multiple Components? Inside the Component, should it be a field or should it be in a Blackboard/Dictionary? 

**Where to process the logic?** By definition, we know it should be in a System, but should it be in only one or distributed into multiple Systems? should it be in a Script or in multiple Scripts? should I put logic in Components even though I am not supposed to?

It obviously depends but one thing I learned is that you might discover the best place over time. A good thing though is that it is normally easy to decide good first location and change it later. 

_Note: even though it is against the idea of ECS to have logic in components, some times it comes super handy to have helper logic in there in the form of properties, methods or even extension methods._

Having said that, I will share some examples inspired in my experience.
# Too much data in only a component

In a platformer game, the main character walks over platforms and can jump from platform to platform. For this game, we could have just a CharacterComponent with data like movement horizontal speed over platforms and air, jump speed and initial impulse when jumping, etc:

```csharp
struct CharacterComponent {
  bool inContactWithPlatform;
  float platformSpeedOnGround;
  float platformSpeedOnAir;
  bool jumping;
  float jumpSpeed;
  float jumpInitialImpulse;
  // others related with the character
}
```

In this solution there might be a System processing the set CharacterComponent and PhysicsComponent (or similar) and in that iteration performing both the movement and jump logic.

A pseudo code system could be something like this:

```csharp
void Update() {
  foreach (e in set(CharacterComponent c, PhysicsComponent p, MovementComponent m)) {
    updateJumping(c, p) // apply vertical movement with physics if jumping
    updatePlatformer(c, p, m); // detect if over platform or not and set in another component 
  }
}
```

That solution isn't wrong, those values need in some way to work together, you cant have a jump if you cant place the character (or whatever) over platforms. There are code smells here, for example:

* Variables with prefixes.
* System is doing multiple logic together (maybe with lots of ifs), and that logic probably acting on part of the data, and other logic on the other part. 
* I can't easily reuse only part of the data (and logic). I might want to have something that can be over platforms and fall, but can't Jump, so I want to use only part of the data. I could always use a bool like `jumpEnabled` for example, to say the unit has no jump, but that means it is processed anyway by systems to compare and do nothing. It is better to not have that Component if not used.

_Note: The first code smell is common in OOP too and one way to attack it is to extract a class. In ECS a good practice is to extract a component and separate systems._

We could separate in different Components, for example:

```csharp
struct CharacterComponent {
  // others related with the character
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
And now two systems:

```csharp
foreach (e in set(JumpComponent j, PhysicsComponent p)) {
  updateJumping(j, p) // apply vertical movement with physics if jumping
}

foreach (e in set(PlatformerComponent p, PhysicsComponent ph, MovementComponent m)) {
  updatePlatformer(p, ph, m); // detect if over platform or not and set in another component 
}
```
 
In my case, working on an Endless Runner 2d game that uses Physics2d I reused a JumpComponent from the 2.5d Beat'em Up which was using Physics3d. The changes were in the systems, I added more systems or added some data in the component extending its reusability.

# Data too separated, might join in one component

# Collection of data of the same type

* TODO: The Abilities/Targetings special case (having list of data in one component to simulate having multiple components of one type) multiple Scripts? 

# Too much logic in one system

* TODO: code smell too much logic, too much components at the same time, or pattern want to order logic

* TODO: Separate system in multiple small steps, for example, instead of updating the position directly in the jumpsystem, just update another component.



# Distributing logic in different Systems

* TODO: explain separating iterations of tuples and be able to optimize by not iterating on things...

# Systems delay of one or more frame for data

One great thing about ECS is that easily allow ordering logic, you can be sure some logic runs before or after another and this is really important to improve determinism, debug, etc.

However, there is also a drawback when a system or a script want to react as soon as possible to some data change but it might need to wait to the next update. 

In my experience, it general (for most of the systems) is not an big issue but for some others it is, mainly when related with things the player can see. For example, when firing a projectile maybe one system creates the projectile entity and if the model loads in the next frame, the player might notice a visual delay. 

Another case is when systems not ordered properly, they might need to wait even 2 frames for some data, this happens in this case:

```csharp
public class World() {
  public void Update() {
    SystemC.update();
    SystemB.update();
    SystemA.update();
  }
}

```

Lets assume those systems depend on data in order, SystemA produces some data, SystemB updates it and finally SystemC does the last logic. In that world, that data is processed in 3 different frames. The fix here is to reorder systems, that might be easy at first when having low count of systems but might become difficult over time. The good thing though is not all systems depend on all systems so changing order of small chunks of systems don't normally break anything.

Now, back to the render issue, my approach for that case is to react as soon as possible to create everything and to separate systems in different Unity events (FixedUpdate, Update and LateUpdate). 

The system API has a create/destroy callbacks when a new entity is created to configure it as soon as possible, and then the render systems, that run on the LateUpdate, might complete configuration (set positions, render order, etc) and render it.

EXAMPLE MODELS

EXAMPLE EVENTS

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

There is a common case where you need to run some logic after other 

Another thing to recap is, when a System iterates over entities with ComponentA and ComponentB, it doesn't care about other components

# Tips to decide



EXAMPLE OR LINK TO EXAMPLE HERE



For example, in the game I am workin right now:

EXAMPLE ABOUT MODEL AND SPINE?

Systems are good to define general logic, things that you want to execute over any entity that matches the criteria of components. For example, in order to simulate gravityScale when using Unity Physics 3d I have a GravitySystem that adds gravity acceleration to entities with the GravityComponent. 

THINKG A BETTER GAME EXAMPLE

However, there are cases where you need specific logic that only one entity at a specific time of the game should do. In those cases I like to use some kind of scripting solution[^2], similar to MonoBehaviours, things that only run for one entity.

Scripting
- reuse controller instances
- data stored in entities through components or through blackboard component
- real examples from the game
- the examples from the endless/platformer blogpost are in controllers/scripts

Some times what logic should be in a System and what logic should be in a Script is not so obvious, and that is something I want to talk about in this blog post. .

# My process when I don't know the best place for code or logic

// TODO: explain this more in detail and maybe with examples.

* I start with scritps both data and logic, then I want to read the data in somewhere else, so I move the data to components, then I want to reuse the logic, I move part of the logic to systems.

In my experience, each time I detected some logic could be reusable and spent time moving logic from scripts to systems at the end of the day it always felt the right thing to do.

# Examples

* In IMI we had the formation as a class, SquadComponent had a Formation class and there we performed all the logic to locate units from a squad in a position. It worked but we had some issues... We later moved it to FormationComponent and moved the members's position calculation to a system.

* In IMI, show in fog while targeting (vultures sniper tower) is a super specific system only used by that tower

# Conclusions

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