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

[TDD (Test Driven Development)](https://en.wikipedia.org/wiki/Test-driven_development) is a technique of writing tests before writing actual code, after those tests fail write the code to make them pass and finally refactor the code to improve it, and repeat.

In this blog post I want to go from that theory, to this in practice:

<div class="post-image">
<video width="100%" controls>
  <source src="/assets/tdd-nekoplatformer-01.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>An example of my tests using the Test Runner in Play Mode</span>
</div>

I've been using TDD for years in different projects. I feel it has great value as a design process that could be applied at different layers of abstraction, not only for code.

Matching its definition, I normally start thinking on how I want to validate a new content or feature. Then, I create the context and logic to validate it and when that fails I move into implementing the feature. Sometimes it can be validated using one or more unit test, others, it might need an entire scene with a complex setting. 

## Example: adding double jump to character (abstract)

At some point of the development of a platformer game we decided we want to add a double jump to the main character. 

Asking myself different questions helps me in defining how I want this feature to behave and the different cases I could use to validate what I want.

* Do I want to be able to jump at any time during the jump?
* Do I want to allow a jump if I fall from a platform or double jump in that case? 
* Do I want the second jump to be the same as the initial one or different (faster, less height, etc)?

This process help in the design of the feature, the game and the code.

For the first question, suppose I only want to allow a double jump if the player presses the jump button while the character is going up, before starting to fall. 

To validate that, I want to have a test where the character jumps by pressing jump button and press jump button again before falling and see the character jump again. 

Also, I need a test where the character jumps by pressing the jump button and press the jump button again but after it started falling and see it doesn't jump the second time. 

# How am I validating that in my games 

## Setting up the context 

Suppose I am adding a new feature that if the character is not over a platform it should fall until it touches one. 

My test will be something like this, the character starts over a platform, near a corner, moves right, validate is falling.

First, I set the context of the test, so I create the level and put the character in a corner.

<div class="post-image">
 <img src="/assets/tdd-nekoplatformer-screenshot-01.png" width="400px"/>
<span>Character initial location for the test.</span>
</div>

To do that, the game and engine must support a way to configure these things. Unity for example supports that by instantiating a prefab and locating it in a position, override values, etc.

In my case, I am using en ECS framework and I have an abstraction layer to instantiate entities and override values, I call it Level Design elements and they normally are something like "Spawn a new Entity using this Definition here and override these values".

<div class="post-image">
 <img src="/assets/tdd-nekoplatformer-screenshot-02.png" width="100%" />
<span>It shows my custom level design elements that allow me to customize entities creation.</span>
</div>

## Defining the actions to validate the test

After having the initial context, I define the actions using my Triggers' Logic (which is like a simplified tool to control execution using Game Objects) in order to create the test.

The Triggers' Logic is something I made to create logic composing Game Objects. There is a [blog posts I wrote at Gemserk](https://blog.gemserk.com/2017/03/27/playing-with-starcraft-2-editor-to-understand-how-a-good-rts-is-made/) explaining where the inspiration came from and its first iterations. It is pretty similar to what I created at Ironhide when working on Iron Marines but I've to code it from zero and made different design decisions in that process.

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

Even though my test cases are automated, they are not completely automatic since I validate the results manually, for example, watching the character falling for the previous case. This obviously doesn't scale well when having multiple test cases and when working with other developers.

_Note: I even have "tests" that I use to validate visual effects working as expected and if I like them or not. The cycle here is to modify the effect, play the scene, if I like it, then continue with another thing, if I don't like it, then modify the effect until I do._

<div class="post-image">
<video width="100%" controls>
  <source src="/assets/tdd/tdd-vfx-validation.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>It shows the scene I used to validate a bit the teleport effect</span>
</div>

## Automatic validation with assert actions

Recently, I started working in filling that missing part of my workflow: the automatic validation. To do that, I created new assert actions to validate state, for example: "this entity should be around this position". 

In the case of the previous example, the new action asserts the entity is falling by comparing it velocity with a range of negative values.

<div class="post-image">
<video width="100%" controls>
  <source src="/assets/tdd/tdd-testcase-automatic-validation.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>Shows the same test running but now automatic validation.</span>
</div>

Even though I now have automatic validation, I still have to go test by test to validate it each time I change something, I want an easy way to run them all. 

## Integrate with Unity Test Runner

Unity comes with a Test Runner that allows you to run from simple unit tests to more complex ones that require the runtime initialized and to execute over time.

The objective here is to have my test cases listed in the test runner and when I run them, it should open each scene, activate the test and run it, wait for a result and show it before continuing with next test. The latter are called Play Mode tests.

<div class="post-image">
 <img src="/assets/tdd-nekoplatformer-testrunner.png" width="100%" />
<span>A list of tests in Unity's Test Runner based on my custom tests inside the scene.</span>
</div>

NUnit comes with a way to populate tests parameters by using attributes. In my case I am using [`ValueSource`](https://docs.nunit.org/articles/nunit/writing-tests/attributes/valuesource.html) with custom data with the name test, the time scale to use, etc. One good thing about the Test Runner is that automatically [considers this test with parameters](https://docs.unity3d.com/Packages/com.unity.test-framework@1.3/manual/reference-tests-parameterized.html) and shows them in the UI.

Since these are Play Mode tests there are some limitations: my tests should be on Scenes included in the build (could be excluded from a release build) and the Editor's API can't be used. 

To generate the custom data for the test parameters, I pre process the tests scenes and create an asset in the Resources folder (in order to be found in runtime) with all test cases, during edit time.

<div class="post-image">
 <img src="/assets/tdd-nekoplatformer-testasset.png" width="500px" />
<span>The asset in the Resources folder for the test executor to use as parameters.</span>
</div>

Now, to populate the parameters the assets is loaded and the custom data is returned by this method:

```csharp
public static TestData[] GetTestCases()
{
  var testsAsset = Resources.Load<TestDefinitionAsset>("Tests");
  return testsAsset.testCases.ToArray();
}
```

And here is the code that runs each test case:

```csharp
[UnityTest]
public IEnumerator RunTests([ValueSource(nameof(GetTestCases))] TestData testData)
{
  // set state
  TestRunnerState.message = null;
  TestRunnerState.currentState = TestRunnerState.State.Starting;
  
  yield return SceneManager.LoadSceneAsync(testData.sceneName, LoadSceneMode.Single);
  
  Time.timeScale = testData.timeScale;
  
  TestRunnerState.currentState = TestRunnerState.State.Running;
  
  var testCase = GameObject.FindObjectsByType<TestCase>(FindObjectsInactive.Include, FindObjectsSortMode.None)
      .First(t => t.gameObject.name.Equals(testData.testCase, StringComparison.OrdinalIgnoreCase));
  
  testCase.gameObject.SetActive(true);

  TestRunnerState.currentRunTime = 0;

  if (testData.timeout < 0)
  {
    yield return new WaitWhile(() => TestRunnerState.currentState == TestRunnerState.State.Running);
  }
  else
  {
    while (TestRunnerState.currentState == TestRunnerState.State.Running)
    {
      yield return new WaitForSeconds(0.1f);
      TestRunnerState.currentRunTime += 0.1f;

      if (TestRunnerState.currentRunTime > testData.timeout)
      {
        TestRunnerState.Fail("Timeout");
        break;
      }
    }   
  }

  var testResult = TestRunnerState.currentState;
  
  TestRunnerState.currentState = TestRunnerState.State.None;
  Time.timeScale = 1;
  
  if (testResult == TestRunnerState.State.Pass)
  {
    Assert.Pass(TestRunnerState.message);
  }
  else if (testResult == TestRunnerState.State.Fail)
  {
    Assert.Fail(TestRunnerState.message);
  }
}
```

It basically loads the scene where the test case is, initializes test state and variables (to validate later), find the test and activate it, wait for a result or a timeout.

One interesting point here is, since I am using FixedUpdate for most of my important logic, it is possible to speed up the execution by modifying the time scale. If something fails, I can go to the specific test and manually run it to work on fixing the issue. 

# Test life cycle

What happens if the game changes? like I don't want double jump anymore or I want it different.

As I said at the beginning, I consider TDD mainly design technique and that means I don't normally care so much about the tests after I designed, implemented and validated what I wanted. 

There might be different cases here:

* The test fails because it has no more value for the game/engine, then I remove it.
* The test fails because the feature/mechanic changed, depending how much, I could consider adjusting the test before changing the mechanic, that means, using TDD for the change.
* The test pass and validate a feature I am not using anymore, then I leave it as it is (I could decide to use it again). If it is too much noise I remove it.
* The test is validating a feature that could be abstracted and decoupled from some specific content, like the feature of double jumping. In that case I could consider making that effort and keep the test and the feature alive.

# Conclusions

Testing like this doesn't replace playing the game and/or testing the feature/content in the proper levels and with the rest of the game content but it helps a lot when working on it in isolation, replicating bugs, etc, increasing the playtime value.

Lets end the blog post with a video.

<div class="post-image">
<video width="100%" controls>
  <source src="/assets/tdd/tdd-nekoplatformer-gameplay.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>Just some gameplay of the game I am working on.</span>
</div>

Hope you liked it, share, love, peace.