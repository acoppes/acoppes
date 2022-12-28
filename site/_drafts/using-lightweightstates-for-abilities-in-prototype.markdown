---
layout: post
title:  "Using lightweight states to create unit abilities for my prototype"
date:   2022-12-28 00:08:30 -0300
excerpt: I share how I am using lightweight states to create abilities for the prototype beatemup/actionrpg I am making right now. 
---

I am testing different abilities for the [prototype]({{ site.baseurl }}/2022/11/22/i-quit-myjob-now-what.html) (which changed a lot visually since I started) I am working, for example a charged dash attack.

<div style="text-align:center;margin-bottom:10px">
<video width="640" height="360" controls>
  <source src="/assets/ability_chargedashattack1.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<br/>
Charged dash attack: it charges a special attack after pressing attack button for a while (cancellable if released before time), and unleashes a directional dash attack on button release.
</div>

To make these abilities, I am using lightweight state machines consisting in states identified by a string that can run in parallel or sequence depending on how you want to use it but it doesn't force one state at a time by design.

The API is something like this, it has an `EnterState(stateName)`, `ExitState(stateName)` and `HasState(stateName)` methods and provides a way to react with callbacks to enter and exit events. Entering the same state twice just resets its timer.

Now, we pseudocode I want to explain the ability I shared before.

```c#
// fields to configure from outside
float chargeTime;
float dashSpeed;
Vector3 direction;
float dashTime;
float attackTime;

OnEnter(string state) {

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

OnExit(string state) {

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

  if (states.InState("ChargeAttack")) {

    if (states.InState("ChargeAttack.Charging")) {

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

    if (states.InState("ChargeAttack.AttackReady")) {

      if (!control.attack.isPressed) {
        states.ExitState("ChargeAttack.AttackReady");
        states.ExitState("ChargeAttack.Attack");
        return;
      }

      return;
    }

    if (states.InState("ChargeAttack.Attack")) {

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

In this case, it uses a state named `ChargeAttack` triggered when `attack` button is pressed and it also uses sub states for each part of the ability.

Enjoy two more ability videos:

<div style="text-align:center;margin-bottom:10px">
<video width="640" height="360" controls>
  <source src="/assets/ability_flyingbomb1.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video>
<br/>
Flying bomb attack: it jumps and flies for some time while firing poop projectiles to the ground.
</div>

<div style="text-align:center;margin-bottom:10px">
<video width="640" height="360" controls>
  <source src="/assets/special_rangesstorm_3.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<br/>
Range attack storm (passive): a small delay after activation, it fires two storm of projectiles in all directions. This one could work as a temporary power up or even as a modifier, like Diablo 2 special enemies with auras and stuff but same mechanics.
</div>

### Conclusion

Some things I want to improve is to start defining the states in a declarative way like "during this state, player cant move" or "during this state, gravity is disabled" which helps to be more data driven and game designer friendly. 

Then, I want to incorporate the sub state concept to the engine itself, right now it is just another state running in parallel and I have to remove them manually on exit parent state.

Another obvious thing is to declare the states names in constants but for now the code is not big nor ugly so I prefer to define it while writing the code.

Anyways, this solution is changing and improving while I design the game and define the engine, but wanted to share what I have right now since I kinda like it for its prototyping simplicity.