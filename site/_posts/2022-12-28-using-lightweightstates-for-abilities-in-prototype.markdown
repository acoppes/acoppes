---
layout: post
title:  "Using states to create unit abilities for my prototype"
date:   2022-12-28 00:08:30 -0300
excerpt: I am using states to create abilities for the actionrpg beatemup prototype I am working on and I share a bit of pseudocode to explain how. 
---

I am testing different abilities for the [prototype]({{ site.baseurl }}/2022/11/22/i-quit-myjob-now-what.html) (visuals changed a lot since I started) I am working on, here is an example of one of them:

<div class="post-video">
<video width="480" controls autoplay>
  <source src="/assets/ability_chargedashattack1.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<br/>
<strong>Charged Dash Attack</strong>: it charges a special attack after pressing attack button for a while (cancellable if released before that), and performs a directional dash attack on release.
</div>

To make these abilities, I have a set of states (identified by a string) that can run in parallel or sequence depending on how you want to use them but it doesn't force to one state at a time by design. By themselves, states do nothing at all but allow controllers (character's logic) to check on them and do custom logic accordingly. 

The API is something simple like this:

```
  void EnterState(string name);
  void ExitState(string name);
  bool HasState(string name);
```

And it also provides a way to react to enter and exit with callbacks.

Here is a pseudocode for the Charged Dash Attack ability similar the one I implemented in the prototype:

```c#
// fields to configure from outside
float chargeTime;
float dashSpeed;
Vector3 direction;
float dashTime;
float attackTime;

OnEnterState(string state) {

  if (state == "ChargeAttack.Charging") {
    direction = control.direction;
    movement.disabled = true;
    animation.Play("ChargedAttackCharging");
  }  

  if (state == "ChargeAttack.AttackReady") {
    spawnChargedVfxOverSelf();
    movement.disabled = true;
  }  

  if (state == "ChargeAttack.Attack") {
    movement.disabled = false;
    movement.speed = dashSpeed;
    movment.direction = direction;
    physics.disabled = true;
    animation.PlayLoop("ChargedAttackDash")
  }  

}

OnExitState(string state) {

  if (state == "ChargeAttack.Charging") {
    movement.disabled = false;
  }  

  if (state == "ChargeAttack.AttackReady") {
    movement.disabled = false;
  }  

  if (state == "ChargeAttack.Attack") {
    physics.disabled = false;
    movement.speed = movement.baseSpeed;
  }  

}

OnUpdate(float dt) {

  if (states.HasState("ChargeAttack")) {

    if (states.HasState("ChargeAttack.Charging")) {

      if (stateTime > chargeTime) {
        states.EnterState("ChargeAttack.AttackReady");
        states.ExitState("ChargeAttack.Charging");
        return;
      }

      if (!control.attack.isPressed) {
        states.ExitState("ChargeAttack");
        states.ExitState("ChargeAttack.Charging");
        return;
      }

      return;
    }

    if (states.HasState("ChargeAttack.AttackReady")) {

      if (!control.attack.isPressed) {
        states.ExitState("ChargeAttack.AttackReady");
        states.ExitState("ChargeAttack.Attack");
        return;
      }

      return;
    }

    if (states.HasState("ChargeAttack.Attack")) {

      targets = FindAttackTargets();
      performDamage(targets);
      
      if (animation.IsPlayingI("ChargedAttackDash") && stateTime > dashTime) {
        movement.disabled = true;
        movement.velocity = 0;
        animation.Play("ChargedAttackEnd);
        return;
      }

      if (animation.IsPlaying("ChargedAttackEnd") && animation.completed) {
        states.ExitState("ChargeAttack.Attack");
        return;
      }

      return;
    }

    return;
  }

  if (control.attack.isPressed) {
    states.EnterState("ChargeAttack");
    states.EnterState("ChargeAttack.Charging");
  }
}

```

In this case, it uses a state named `ChargeAttack` triggered when `attack` button is pressed and it also uses sub states for each part of the ability: charge, ready and attack.

Enjoy two more ability videos:

<div class="post-video">
<video width="480" controls>
  <source src="/assets/ability_flyingbomb1.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video>
<br/>
<strong>Flying bomb attack</strong>: it jumps and flies for some time while firing poop projectiles to the ground.
</div>

<div class="post-video">
<video width="480" controls>
  <source src="/assets/special_rangesstorm_3.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<br/>
<strong>Range attack storm</strong>: a small delay after activation, it fires two storm of projectiles in all directions. Since it doesn't take control of the character, this one could work as a temporary power up or even as a modifier, like Diablo 2 special enemies with auras and stuff but same mechanics.
</div>

### Conclusion

Some of the things I want to try for improvement is to start defining the states in a declarative way like "during this state, player cant move" or "during this state, gravity is disabled" which helps to be more data driven and less imperative. 

Then, I have some doubts but might incorporate the sub state concept to the engine itself, right now it is just another state running in parallel and I have to remove them manually on exit parent state but it goes a bit against being decoupled. An example of what I want is to be able to call `ExitState("SomeState")` and that will call exit to all states starting with `SomeState`, for example: `SomeState.Charging`.

A simpler and obvious thing is to declare the states names in constants but for now the code is not so big so I prefer to define it while writing the code, at least while prototyping.

Anyways, this solution is changing and improving while I design the game and define the engine but wanted to share what I have right now since I kinda like it for its prototyping simplicity.