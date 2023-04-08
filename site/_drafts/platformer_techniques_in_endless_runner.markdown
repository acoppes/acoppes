---
layout: post
title:  "Applying tricks from Platformer games applied to an Endless Runner to improve Player's experience"
date:   2023-04-08 00:08:30 -0300
excerpt: "There are multiple tricks like Jump Buffering that can be applied in endless runners as well, I want to share here what am I doing for the game I am working on and how."  
author: Ariel Coppes
tags:
  - techniques
  - howto
  - gameplay
---

{{page.excerpt}}

GAME GIF

### Introduction

This [page](http://www.davetech.co.uk/gamedevplatformer) shows a great example of general mechanics and tricks used to improve player's experience in platformer games. These are the ones I am using:

* **Early Fall**: Player descends as soon as jump button is let go ending jump early.
* **Jump Buffering**: When the character is already in the air pressing jump moments before touching the ground will trigger jump as soon as they land. 
* **Predict Ground Collision**: When the character is already in the air pressing jump and there is a platform near and below, it can jump as it is already on the ground (this one is mine).
* **Coyote Time**: Jump still triggered a few frames after running off a ledge.
* **Catch Missed Jumps**: If a player doesn’t quite make a jump either lift them up a few pixels or change the collision mask so their feet don’t hit the wall. 

### Early fall

ANIMATED GIF WITH DIFFERENT JUMPS (small, bigger, etc)

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

ANIMATED GIF WITH THE OTHER TECHNIQUE DISABLED AND WITH BIG INPUT BUFFER TO SHOW EXAMPLE

When developing the [beatemup prototype](https://arielsan.itch.io/beatemup) I created an Input Buffer to process all the player actions including combos. It ended up being super useful for all the other mini games I worked on, including the endless runner game. What I do is I just decide when to read from the buffer or when to read directly from the control actions depending on what I want case by case.

So, to start a jump, I check the general input buffer to see if there is a jump there, so when the other conditions met, for example being over ground, and there are no other just in time actions, then I start a jump. The code is something like this:

```csharp
var jumpPressed = control.HasBufferedAction(control.button1);

if (jumpPressed && jumpComponent.currentJump < jumpComponent.totalJumps)
{
    // this clears the buffer to not process again
    control.ConsumeBuffer();
    EnterJumping(world, entity);  
    return;
}
```

### Predict Ground Collision

ANIMATED GIF SHOWING THE OVERLAP COLLIDER CHECK

This technique kinda overlap with the previous one. The ideas is to check for the ground to be near when the character is falling and the player presses the jump action, if there is ground near, then resets the jump count and performs a jump from where the character is right now.

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

ANIMATED GIF FOR THIS AIR DELAY?

One drawback of this technique is that allows a higher jump by letting the player jump before touching ground but feels super responsive and I completely prefer that for this game. I tried also projecting the position to the ground 

### References

- [Platforming Tips and Tricks](http://www.davetech.co.uk/gamedevplatformer)