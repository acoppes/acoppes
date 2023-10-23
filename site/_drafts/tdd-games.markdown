---
layout: post
title:  "How I follow a Test Driven Development approach to make games"
# date:   2022-11-22 00:08:30 -0300
excerpt: This blogpost covers how I am following a Test Driven Development approach when making games and which tools I am using. 
author: Ariel Coppes
tags:
  - tdd
  - unity
  - howto
image:
  path: /images/ecs-post-preview.jpg
  height: 100
  width: 100
---

{{page.excerpt}}

# Introduction

[TDD (Test Driven Development)](https://en.wikipedia.org/wiki/Test-driven_development) is a code and design technique of writing tests before writing actual code, after those tests fail write the code to make them pass and finally refactor the code to improve it. That cycle repeats over and over, like a spiral.

In this blog post I want to go from that theory, to this in practice:

<div class="post-image">
<video width="100%" controls>
  <source src="/assets/tdd-nekoplatformer-01.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>An example of my tests using the Test Runner in Play Mode</span>
</div>

I've been using it for years in different environments and in different projects. It's main value to me is as a design process and that could be applied at different abstraction layers, not only for code.

Matching its definition, I normally first start thinking on how I want to validate a new content or feature, then I work creating the context to validate that (the test) and when it fails start working on the implementation. Sometimes it could be just code, just using the unit test framework, others it could be an entire scene with a complex setting. 

## Example: adding double jump to character (abstract)

Suppose I want to add a double jump to the main character of a platformer game. Here I could consider different cases that also drive in some way the exact double jump I want. Some questions I ask to myself could be: 

* Do I want to be able to jump at any time during the jump?
* Do I want to allow a jump if I fall from a platform or double jump in that case? 
* Do I want the second jump to be the same as the initial one or different (faster, less height, etc)?

These questions and how I want to validate them are shaping the design of the game and the code.

For the first question, suppose I only want to allow a double jump if the player presses the jump button while the character is going up, before starting to fall. 

To validate that, I want to have a test where the character jumps by pressing jump button and press jump button again before falling and see the character jump again. And I also want to have a test where the character jumps by pressing the jump button and press the jump button agian but after falling and see it doesn't jump again. 

Now, I also want to make the second jump more powerful than the first one. For that, and using a previous test I did to make sure a jump reaches the max height X, I will now make a test case where the a double jump has to reach more than 2X of max height.

# How am I validating that in my games 

## Setting up the context 

First, I set the context of the test. For example, I want the character to be over a corner so when it moves right it falls.

<div class="post-image">
 <img src="/assets/tdd-nekoplatformer-screenshot-01.png" width="400px"/>
<span>Character initial location for the test.</span>
</div>

To do that, the game and engine must support a way to configure these things. Unity for example supports that by instantiating a prefab and locating it in a position, override values, etc.

In my case, I am using en ECS framework and I have an abstraction layer to instantiate entitties and override values, I call it Level Design elements and they normally are something like "Spawn a new Entity using this Definition here and override these values".

<div class="post-image">
 <img src="/assets/tdd-nekoplatformer-screenshot-02.png" width="100%" />
<span>It shows my custom level design elements that allow me to customize entities creation.</span>
</div>

## Defining the actions to validate the test

After having the initial context, I define the actions using my Triggers' Logic (which is like a simplified tool to control execution using Game Objects) in order to create the test.

The Triggers' Logic is something I made to create logic composing Game Objects. There is a [blogpost I wrote at Gemserk](https://blog.gemserk.com/2017/03/27/playing-with-starcraft-2-editor-to-understand-how-a-good-rts-is-made/) explaing where the inspiration came from and its first iterations. Even though it might look pretty similar to what I created at Ironide when working on Iron Marines, I coded it from zero and made different design decisions.

For example, the actions for the previous test case could be: 
  1. Move the character to the right.
  2. Wait some time.
  3. Check the character is falling.

<div class="post-image">
<video width="100%" controls>
  <source src="/assets/tdd/tdd-testcase-manual-validation.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>Shows the test running with no automatic validation but showing visually what is expected (I already have the code to make the character fall implemented).</span>
</div>

Even though my test cases are automated, they are not completely automatic since I validate the results manually, for example, watching the character falling for the previous case. This obviously don't scale well when having multiple test cases and when working with other developers.

I even have "tests" that are an isolated case that I use to validate a visual effect is working as expected as well as if I like it or not. The cycle here is to modify the effect, play the scene, if I like it, then continue with another thing, if I don't like it, then modify the effect until I like it.

<div class="post-image">
<video width="100%" controls>
  <source src="/assets/tdd/tdd-vfx-validation.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>It shows the scene I used to validate a bit the teleport effect</span>
</div>

## Automating it a bit more with the Test Runner?

Recently, I started working in filling that missing part of my testing workflow, the automatic validation. To do that, I created new Triggers' Logic assert actions to validate state, for example "this entity should be around this position", and also integrated it with Unity's Test Runner. In the case of the previous example, I have an assert action to validate the vertical velocity is negative and in a range.

<div class="post-image">
<video width="100%" controls>
  <source src="/assets/tdd/tdd-testcase-automatic-validation.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>Shows the same test running but now automatic validation.</span>
</div>

That works well but the next natural step is to integrate it with the Test Runner.

Unity comes with a Test Runner that allows you to run from simple unit tests to more complex tests that require the Unity's runtime initialized and to execute over time. The latter are the Play Mode tests.

When working at Ironhide Games Studio I managed to make a way to detect scene test cases and run all of them automatically from the test runner and that is what I wanted to replicate here.

The objective is to have my test cases listed in the test runner and when I run them, it should open each scene, activate a test and run it, wait for a result, show it and continue with next test.

<div class="post-image">
 <img src="/assets/tdd-nekoplatformer-testrunner.png" width="100%" />
<span>A list of tests in Unity's Test Runner based on my custom tests inside the scene.</span>
</div>

Combining the NUnit attributes and generating a small framework around my Triggers' Logic, I can add my test cases to the list of test to run in the Test Runner and execute them in a specific way in order to validate one by one my tests and show the results there. 

TODO: CODE TO SHOW HOW

Since I am using FixedUpdate for most of my important logic, it is possible to run this kind with increased speed by modifying the timeScale, which is super useful since I don't need to see the test running. If something fails I can go to the specific test and work on that one. 

# Test life cycle

What happens if the game changes? like I don't want double jump anymore or I want it different.

As I said at the beginning, I consider TDD mainly design technique and that means I don't normally care so much about the tests after I desgined, implemented and validated what I wanted. 

There are different cases here:

* The test fails because it has no more value for the game/engine, then I remove it.
* The test fails because the feature/mechanic changed, depending how much, I could consider adjusting the test before changing the mechanic, that means, using TDD for the change.
* The test pass and validate a feature I am not using anymore, then I leave it as it is (I could decide to use it again). If it is too much noise I remove it.
* The test is validating a feature that could be abstracted and decoupled from some specific content, like the feature of double jumping. In that case I could consider making that effort and keep the test and the feature alive.

# What about unit tests?

I am using Unity's Editor Mode unit tests for part of the code, for example to validate a transformation of data of an ECS system, but most of the time I add the test later to improve code, not so driven by the test first.  

# Conclusions

* Yes, I am validating by senses/eye some cases, not by values, for example, watching the character jumps. It can be imrpoved
* How long do my tests live considering the design of the game could change a lot, for example I could change to not want double jump. 
* The important part is validating small stuff in isolation by making the proper context and work with that.
* This doesn't replace playing the game and/or testing the feature/content in the proper levels, but it helps a lot on working that in isolation, replicating bugs, etc, to improve the value when playing the game.
