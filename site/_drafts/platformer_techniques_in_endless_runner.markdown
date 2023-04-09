---
layout: post
title:  "Mechanics and tricks from Platformers applied to Endless Runners to improve experience"
date:   2023-04-09 00:08:30 -0300
excerpt: "There are multiple mechanics and tricks like Jump Buffering that can be applied in endless runners as well, I want to share here what am I doing for the game I am working on and how."  
author: Ariel Coppes
tags:
  - techniques
  - howto
  - gameplay
---

{{page.excerpt}}

<div class="post-image">
<video width="630" height="350" controls>
  <source src="/assets/endlessrunner-gameplay-01.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>Gameplay video to show how the game looks right now and some of the techniques.</span>
</div>

### Introduction

Endless Runners are a sub genre of Platformers, so it makes sense some of the techniques used to improve player's experience might work as well but not all of them and some might need some adjustment. 

This [page](http://www.davetech.co.uk/gamedevplatformer) shows a great example of general mechanics and tricks used to improve player's experience in platformer games. These are the ones I am using:

* **Early Fall**: Player descends as soon as jump button is let go ending jump early.
* **Jump Buffering**: When the character is already in the air pressing jump moments before touching the ground will trigger jump as soon as they land. 
* **Predict Ground Collision**: When the character is already in the air pressing jump and there is a platform near and below, it can jump as it is already on the ground (this one is mine).
* **Coyote Time**: Jump still triggered a few frames after running off a ledge.
* **Catch Missed Jumps**: If a player doesn’t quite make a jump either lift them up a few pixels or change the collision mask so their feet don’t hit the wall. 

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

Here I consider different things. One of them that I force the jump to be active a minTime if the player presses and releasees the jump butotn in two consecutive frames. This is to avoid almost not jumping. After that minTime, if the player releases the jump button, the character starts falling, giving the player good control of falling over the desired platforms.

### Jump Buffering

<div class="post-image">
<video width="400" height="300" controls>
  <source src="/assets/endlessrunner-jumpbuffer-01.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>With no input buffering the player presses and releases the jump action before ground contact but the character doesn't jump.</span>
</div>

When developing the [beatemup prototype](https://arielsan.itch.io/beatemup) I created an Input Buffer to process all the player actions including combos. It ended up being super useful for all the other mini games I worked on, including the endless runner game. What I do is I just decide when to read from the buffer or when to read directly from the control actions depending on what I want case by case.

So, to start a jump, I check the general input buffer to see if there is a jump there, so when the other conditions met, for example being over ground, and there are no other just in time actions, then I start a jump.

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

This technique kinda overlap with the previous one. The ideas is to check for the ground to be near when the character is falling and the player presses the jump action, if there is ground near, then resets the jump count and performs a jump from where the character is right now.

Why using both? because this one works well only when there is ground below, the input buffer works well after performing dash or when you are almost over a platform. 

<div class="post-image">
<video width="400" height="300" controls>
  <source src="/assets/endlessrunner-predict-02.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>No input buffer but ground prediction enabled, and distance a bit exaggerated for the video.</span>
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

The game has air jumps, what I do here is to reset the air jump count if I detect ground nearby and let the air jump logic process normally. 

There is also an air jump delay here, it is a small delay to avoid jumping and jumping again in the next frame which didn't look well and it wasn't so useful, it felt like a bug. But, since it checks from the buffer, it starts the air jump as soon as it can and the player might not notice the delay.

One drawback of this technique is that allows a higher jump by letting the player jump before touching ground but feels super responsive and I completely prefer that for this game.

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

However, I recently added an optional feature (currently enabled) of not losing the first jump on falling from platforms which feels better for endless runners. With this feature enabled there is no value of using coyote time.

### Catch Missed Jumps

<div class="post-image">
<video width="400" height="300" controls>
  <source src="/assets/endlessrunner-catchmissed-01.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>With the feature disabled the character almost reach the platform but fails.</span>
</div>

For this one what I do is, in the moment the character collides with a wall I check if the collider can be relocated over the ground considering a max distance from the contacts between the character and the wall. If there is an space, then I relocate the character instantly and put a particle over to hide a bit the jump. 

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

There is a thing I want to improve here is to not stop the character when it collides but right now I am reacting one frame later to the collision so the velocity is 0 in x for one or more frames and it feels a bit strange when playing the game. 

### Links

- [Platforming Tips and Tricks](http://www.davetech.co.uk/gamedevplatformer)
- [Vertx.Debugging](https://github.com/vertxxyz/Vertx.Debugging)