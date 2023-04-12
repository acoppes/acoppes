---
layout: post
title:  "Mechanics and tricks from Platformers applied to Endless Runners to improve experience"
date:   2023-04-10 00:10:15 -0300
excerpt: "There are multiple mechanics and tricks from Platformer games that can be applied in Endless Runners as well. I want to share here what am I doing for the game I am working on and how."  
author: Ariel Coppes
tags:
  - techniques
  - howto
  - gameplay
image:
  path: /assets/endlessrunner-screenshot-01.png
  width: 320
  height: 180
---

{{page.excerpt}}

<div class="post-image">
<video width="100%" controls>
  <source src="/assets/endlessrunner-gameplay-01.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>Gameplay video of the Endless Runner game I am developing.</span>
</div>

Even though it is a WIP, you can [PLAY IT HERE](https://arielsan.itch.io/platformergame).

### Introduction

Since Endless Runners are a sub genre of Platformers it makes sense that some of the techniques used to improve player's experience might work as well. However, not all of them work and some might need some adjustments before using them. 

David Strachan summarizes the most common used mechanics and tricks in his blog post [Platforming Tips and Tricks](http://www.davetech.co.uk/gamedevplatformer). Considering mechanics' names used there, these are the ones I've implemented:

* [**Early Fall**](#early-fall): Player descends as soon as jump button is let go ending jump early.
* [**Jump Buffering**](#jump-buffering): When the character is already in the air pressing jump moments before touching the ground will trigger jump as soon as they land. 
* [**Predict Ground Collision**](#predict-ground-collision): When the character is already in the air pressing jump and there is a platform near and below, it can jump as it is already on the ground (this one is mine, not listed in the reference).
* [**Coyote Time**](#coyote-time): Jump still triggered a few frames after running off a ledge.
* [**Catch Missed Jumps**](#catch-missed-jumps): If a player doesn’t quite make a jump either lift them up a few pixels or change the collision mask so their feet don’t hit the wall. 

### Early fall

<div class="post-image">
<video width="400" height="300" controls>
  <source src="/assets/endlessrunner-earlyfall-01.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>Different jumps by releasing the jump action at different times.</span>
</div>

All the movement in the game is processed by one controller with multiple [states](2022/12/28/using-states-for-abilities-in-prototype.html). In the case of jumping, the logic is something like this:

```csharp
if (states.TryGetState("Jumping", out state))
{
  if (!control.button1.isPressed)
  {
    states.ExitState("Jumping/GoingUp");
  }

  var jumping = state.time < jumpComponent.minTime;

  if (states.HasState("Jumping/GoingUp") && control.button1.isPressed)
  {
    jumping = true;
  }

  if (jumping)
  {
    velocity.y = jumpComponent.speed;
  }
  
  if (state.time > jumpComponent.maxTime || !jumping)
  {
    ExitJumping(world, entity);
    EnterFalling(world, entity);
  }
  
  jumpComponent.activeTime += dt;

  return;
}
```

Here I consider different things. One of them that I force the jump to be active a minTime if the player presses and releases the jump action in two consecutive frames. This is to avoid almost not jumping. The other is that, after the minTime, if the player releases the jump action, the character starts falling as soon as possible, giving the player good control of falling over the desired platforms.

*Side note: I am using the plugin [Vertx.Debugging](https://github.com/vertxxyz/Vertx.Debugging) which implements both Physics2d and Physics API with Draw methods to visualize collisions, etc.*

### Jump Buffering

<div class="post-image">
<video width="400" height="300" controls>
  <source src="/assets/endlessrunner-jumpbuffer-01.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>With no input buffering the player presses and releases the jump action before ground contact but the character doesn't jump.</span>
</div>

When developing the [beatemup prototype](https://arielsan.itch.io/beatemup) I implemented an Input Buffer to process all the player actions in order to perform combos. It ended up being super useful for all the other prototypes and mini games including the current one. What I normally do is, depending each mechanic, to read from the buffer or directly from the control actions.

In the case of starting an new jump I check the input buffer to see if there is a jump action there, so when the other conditions are met, for example being over ground, then I start a jump. There are cases where I prefer to start a dash instead of a jump but that is defined by the priority of the character's actions, and might also depend on checking the most recent action in the buffer as well (case by case).

<div class="post-image">
<video width="400" height="300" controls>
  <source src="/assets/endlessrunner-jumpbuffer-02.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>With input buffer I can check if the jump or other actions were pressed and process it after touching the ground.</span>
</div>

```csharp
var jumpPressed = control.HasBufferedAction(control.button1);

if (jumpPressed && jumpComponent.currentJump < jumpComponent.totalJumps)
{
    // this clears the buffer to not process again
    control.ConsumeBuffer();
    EnterJumping(world, entity);  
}
```

### Predict Ground Collision

<div class="post-image">
<video width="400" height="300" controls>
  <source src="/assets/endlessrunner-predict-01.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>No input buffer and no prediction.</span>
</div>

This technique overlaps with the previous one. The idea is to check, when the character is falling and the player presses the jump action, if there is ground nearby. If there is, then it resets the jump count like it touched the ground and performs a jump from where the character is right now.

Why using jump buffer and this technique? because this one has better response but works well only when there is ground below while the input buffer works also after performing dash or when you are almost over a platform. 

<div class="post-image">
<video width="400" height="300" controls>
  <source src="/assets/endlessrunner-predict-02.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>No input buffer but ground prediction enabled.</span>
</div>

```csharp
if (control.HasBufferedAction(control.button1) && jumpComponent.airJumpDelay.IsReady)
{
  if (physics2dComponent.collideWithStaticCollider is CapsuleCollider2D capsuleCollider)
  {
    var offset = capsuleCollider.bounds.size.y * platformer.fallingJumpGroundDetectionOffset;

    var count = DrawPhysics2D.CapsuleCastNonAlloc(
        controllerEntity.position.value.ToVector2() + capsuleCollider.offset, capsuleCollider.size,
        CapsuleDirection2D.Vertical, 0, Vector2.down, raycasts, offset, LayerMask.GetMask("StaticObstacle"));

    if (count == 1)
    {
      jumpComponent.currentJump = 0;
      jumpComponent.airJumpDelay.Fill();
    }
  }

  // this is the code for the "air jump"
  if (jumpComponent.currentJump < jumpComponent.totalJumps && jumpComponent.airJumpDelay.IsReady)
  {
    control.ConsumeBuffer();
    
    ExitFalling(world, entity);
    EnterJumping(world, entity);
    
    return;
  }
}
```

*Side note: During falling state there is a small delay to avoid jumping and jumping again in the next frame. This could lose a jump action if pressed after a release before that delay but since it checks from the buffer it starts the next jump as soon as possible.*

One issue of this technique is that allows a higher jump by letting the player jump before touching ground but I preferred responsiveness over consistency for this game.

### Coyote Time

<div class="post-image">
<video width="400" height="300" controls>
  <source src="/assets/endlessrunner-coyotetime-01.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>With no coyote time enabled.</span>
</div>

To implement this one I store the time since ground contact was lost and compare that with a max valid time when jump action was pressed to consider a valid jump or not. I added something like this to the previous jump code.

```csharp
// timeSinceGroundContact is always 0 while in contact with ground
if (timeSinceGroundContact < jumpComponent.coyoteTime)
{
  control.ConsumeBuffer();
  EnterJumping(world, entity);
}
```

<div class="post-image">
<video width="400" height="300" controls>
  <source src="/assets/endlessrunner-coyotetime-02.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>With coyote time enabled.</span>
</div>

I also changed to not enter falling state during that time either which previously entered automatically when ground contact was lost.

However, I recently added an optional feature (currently enabled) of not losing any jump when falling from platforms which feels better for endless runners but makes coyote time useless at the same time.

### Catch Missed Jumps

<div class="post-image">
<video width="400" height="300" controls>
  <source src="/assets/endlessrunner-catchmissed-01.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>With the feature disabled the character almost reach the platform but fails.</span>
</div>

For this one what I do is, in the moment the character collides with a wall I check if the collider can be relocated over the ground considering a max distance from the contacts between the character and the wall. If there is an empty space, then I relocate the character instantly and put some particles over to hide the teleport. 

<div class="post-image">
<video width="400" height="300" controls>
  <source src="/assets/endlessrunner-catchmissed-02.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>Now with the feature on, the character is adjusted to continue running over the platform.</span>
</div>

```csharp
 if (foundContact && controllerEntity.stateController.CanInterrupt(world, entity, this))
{
  var distanceToGround = topContactPoint.point.y - controllerEntity.position.value.y;
  
  if (distanceToGround < adjustThreshold)
  {
    // I move it a bit to the right here
    climbPosition = topContactPoint.point + direction.ToVector2() * 0.1f;

    // this is just debug
    D.raw(new Shape.Ray2D(topContactPoint.point, topContactPoint.normal), Color.green);
    
    // and here I check a bit up to avoid colliding with the ground when checking.
    var collider = DrawPhysics2D.OverlapCapsule(climbPosition + capsuleCollider.offset + new Vector2(0, 0.1f), capsuleCollider.size,
        CapsuleDirection2D.Vertical, 0, LayerMask.GetMask("StaticObstacle"));

    // we found an empty space over the platform
    if (collider == null)
    {
        EnterClimb(world, entity);
    }
  }
}
```

One thing to improve is to not stop the character when it collides with the wall but right now I am reacting one frame later to the collision so the velocity is 0 in x for one or more frames and doesn't feel good when playing the game. 

*Side note: Initially there was a climb animation when the character reached a corner and during that time the character didn't move until the climb was over. Animation  could be cancelled by jumping or dashing. That felt good when the game was still a platformer but for a runner felt really bad since it was losing the momentum and also felt like something wrong happened so I reduced as much as possible to don't feel like the movement stopped at all.*

### Bonus track

The game started as a Platformer and then mutated to be an Endless Runner, that is why I implemented some features (like climb walls) that then lost value and were removed. Here is a video of the game before switching genres.

<div class="post-image">
<video width="100%" controls>
  <source src="/assets/endlessrunner-platformer-01.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>Gameplay video of the platformer before.</span>
</div>

As always I hope you liked the blog post and I really appreciate if you share it, thanks!