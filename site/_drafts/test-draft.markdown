---
layout: post
title:  "Design decisions when building games using ECS"
# date:   2022-11-22 00:08:30 -0300
excerpt:  When using ECS (Entity Component System), which data goes in which Component and why?, which logic goes in which System?, in the case of using a Scripting framework, which logic goes in scripts instead of systems?. This blog post tries to share how I approach these decisions after years of using different ECS solutions for different games. 
author: Ariel Coppes
tags:
  - personal
  - update
---

{{page.excerpt}}

# Background

My experience with ECS goes back to 2010 when we started making games at Gemserk with [@rgarat](https://twitter.com/rgarat), we used [Artemis](https://blog.gemserk.com/2011/11/13/scripting-with-artemis/) at that time and we even made the port for mobile devices of Clash of the Olympians using it. Over the years I used different ECS solutions and I even developed my own for [Iron Marines Invasion](https://www.ironmarinesinvasion.com/)[^1]. Nowadays, I use the Community maintained fork of [LeoECS](https://github.com/LeoECSCommunity) for my games.

I assume you already know about ECS but here is a great [source of information](https://github.com/SanderMertens/ecs-faq) as introduction. If you come from an OOP background you have to [change your mindset](https://github.com/SanderMertens/ecs-faq#how-is-ecs-different-from-oop) to maximize the value of using ECS. 

Even though Systems implement the logic, they depend directly on which components the entity has and their data. This means the behaviour of the application in ECS is mostly data driven: adding or removing a component or changing its data will change which systems execute and what they do. 

For me, ECS is a programming paradigm and a way to structure the code in order to achieve big performance improvements (different solutions might go deeper in this aspect). 

To understand the examples of this blog post, I first have to tell you that I use a scripting framework similar to the [one we used at Gemserk]((https://blog.gemserk.com/2011/11/13/scripting-with-artemis/)), so I can have specific logic in Scripts that runs only for some entities. Scripts can have readonly and mutable data but the second is discouraged (move that data to Components is recommended). 

**Where to put data?** The quick answer for ECS is to put it in a Component. But, should it be in only one or distributed in multiple Components? Inside the Component, should it be a field or should it be in a Blackboard/Dictionary? 

**Where to process the logic?** The quick answer is to put it in a System, but should it be only one or distributed in multiple Systems? should it be in a Script or in multiple Scripts? should I put logic in Components even though I am not supposed to?

Obviously, it depends. But, one thing I learned is that you might discover the best place over time. It is not always easy to decide the best place in the first try but most of the time is easy to decide a first location. 

But having said that, I will share some examples inspired in my experience.

# Distributing data in multiple components

In a platformer game, the main character walks over platforms and can jump from platform to platform. For this game, we could have just a CharacterComponent with data like movement horizontal speed over platforms and air, jump speed and initial impulse when jumping, etc:

```csharp
struct CharacterComponent {
  bool inContactWithPlatform;
  float platformSpeedOnGround;
  float platformSpeedOnAir;
  bool jumping;
  float jumpSpeed;
  float jumpInitialImpulse;
  AnimationCurve jumpCurve;
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

_Note: A common pattern for separating data is when there are prefixes like jumpSpeed, jumpImpulse, jumpCurve, etc. This happens a lot in OOP too, and in there it is common to extract a class with all that data (and related logic). Here the pattern is to extract a component and separate systems._

We could separate in different Components, for example:

```csharp
struct CharacterComponent {
  // others related with the character
}

struct PlatformerComponent {
  bool inContactWithPlatform;
  float speedOnGround;
  float speedOnAir;
}

struct JumpComponent {
  float speed;
  bool jumping;
  float initialImpulse;
  AnimationCurve curve;
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

### Tips to decide data separation



* TODO: The Abilities/Targetings special case (having list of data in one component to simulate having multiple components of one type)
 multiple Scripts? 

# Distributing logic in different Systems

* TODO: explain separating iterations of tuples and be able to optimize by not iterating on things...

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

[^1]: Yes, I tried Unity ECS multiple times during development, it wasn't ready for what I wanted and it was hard to explain to new developers at Ironhide, that is why I decided to avoid it.

[^2]: When we used LibGDX and Artemis at Gemserk we created our [scripting system](https://blog.gemserk.com/2011/11/13/scripting-with-artemis/), it felt like oviously necessary to customize logic.

# My process when I don't know the best place for code or logic

// TODO: explain this more in detail and maybe with examples.

* I start with scritps both data and logic, then I want to read the data in somewhere else, so I move the data to components, then I want to reuse the logic, I move part of the logic to systems.

# Examples

* In IMI we had the formation as a class, SquadComponent had a Formation class and there we performed all the logic to locate units from a squad in a position. It worked but we had some issues... We later moved it to FormationComponent and moved the members's position calculation to a system.

IM examples

* formations stared as logic in component and scripts, moved to systems
* show in fog while targeting (vultures sniper tower) is a super specific system only used by that tower

Tips

* if it is a super transversal feature, like walking, might be in a system
* if it is super specific feature like one entity doing something in specific level, then script
* if you don't know it is normally easier to start as script and scale it to a system, move data first to a component and then move the (or parts of the) logic to one or more systems
* some times for a game a feature is broader than for other games, but if you make it a clean component+system then it is always better

# Conclusions

My most important advice for you is to try to make your solution as refactorable as possible in order to support improving it over time, step by step. How? well, first by exercising code refactor (and in that process, identify places to make refactoring easier), using a good IDE like Rider and making tools to help you. 

By working using the ECS paradigm, there are also code smells and best practices start to arise, and both are related to the ones that happen in OOP but they just require different solutions.
 
* Separate data in different Components to achieve clarity, reusability, separation of concerns.
* Separate logic in different systems, ir order to reduce coupling, improve parallelism and make the code more modular and also to control logic order.

