---
layout: post
title:  "How I use Test Driven Development to make games"
# date:   2022-11-22 00:08:30 -0300
excerpt: This blog post is about using TDD (Test Driven Development) (and testing in general) for game development. It explains my personal process and tools I use for my games that are pretty similar to what I used at Ironhide Game Studio to make Iron Marines and Iron Marines Invasion.
author: Ariel Coppes
tags:
  - tdd
  - testing
  - unity
  - howto
image:
  path: /images/ecs-post-preview.jpg
  height: 100
  width: 100
---

{{page.excerpt}}

# Introduction

[TDD](https://en.wikipedia.org/wiki/Test-driven_development) is a technique of writing tests before writing actual code, after those tests fail write the code to make them pass and finally refactor the code to improve it, and repeat that process.

This is an example of a list of tests running in the Unity's Test Runner.

<div class="post-image">
<video width="100%" controls>
  <source src="/assets/tdd-nekoplatformer-01.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
</div>

I've been using TDD for years in different projects. I feel it has great value as a design process that could be applied at different layers of abstraction, not only for code.

I normally start by thinking on how I want to validate a new content or feature. Then, I create the context and actions (the test) to validate it and when that fails I move into its implementation. Sometimes that could be done using just one unit test, others it need an entire scene with a complex setting. This blog post is about the latter. 

To implement a new content or feature a combination code, configurations and assets is required.

_By feature I normally mean a new horizontal mechanic, for example jumping, and by content I mean a game element that could have a new mechanic, for example an enemy that bounces on the screen._  

## A design example: double jump

Suppose that, at some point of the development of a platformer game, we decided to add a double jump. 

There are multiple ways of adding that feature. Asking myself different questions helps me in defining how I want it to behave and the different cases I could use to validate what I want.

* Do I want to be able to jump at any time during the jump?
* Do I want to allow a jump if I fall from a platform or double jump in that case? 
* Do I want the second jump to be the same as the initial one or different (faster, less height, etc)?

This process is shaping the design of the feature, the game and the code.

For the first question, suppose I only want to allow a double jump if the player presses the jump button while the character is going up, before starting to fall. 

To validate that, I want to have a test where the character jumps by pressing jump button and press jump button again before falling and see the character jump again. And a test where the character jumps by pressing the jump button and press the jump button again but after it started falling and see it doesn't jump the second time. 

It might sound a bit exaggerated to use TDD to implement a jump but I just wanted to show the general idea behind the process (I even don't have a double jump in the game). However, I've used it for a variety of cases, from simple and complex ones. For example, when the player taps the jump button the character should at least jump a height of 1 tile in order to allow moving fast through the level when finding small walls or obstacles. For that case I created a test with the character moving and the jump button is tapped (pressed and released as soon as possible) near a wall of 1 tile and after it failed, I modified the logic and and values to make it pass.

<div class="post-image">
<video width="100%" controls>
  <source src="/assets/tdd/tdd-testcase-minjump.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>A test case to validate that by tapping jump button the character reaches at least one tile.</span>
</div>

# How am I testing 

For this game prototype, the main character has an ability that automatically teleports him to special locations by firing a kunai.

<div class="post-image">
<video width="100%" controls>
  <source src="/assets/tdd/tdd-teleport-feature-01.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>It shows the teleport feature.</span>
</div>

At some point I decided to add a level design element that redirects the kunai when they touch each other. My idea for the test sequence is something like this: the fire button is pressed, a kunai is fired and after it hits the redirect element, the kunai should be moving up.

## Setting up the context 

To create this element I started by first creating the context where I wanted it validated, I put the character and in front of it, a redirect element (using a visual placeholder). 

<div class="post-image">
 <img src="/assets/tdd/tdd-redirect-firstscene-01.png" width="400px"/>
<span>The first context to validate the new feature.</span>
</div>

In order to easily configure context the game and engine must support a way to set it up. Unity for example supports that by instantiating a prefab, locating it in a position and override values, among other useful things.

In my case, I am using en ECS framework and I have an abstraction layer to instantiate entities and override values, I call it Level Design elements and they normally are something like "Spawn a new Entity using this Definition here and override these values".

<div class="post-image">
 <img src="/assets/tdd-nekoplatformer-screenshot-02.png" width="100%" />
<span>It shows my custom level design elements that allow me to customize entities creation.</span>
</div>

## Defining the actions to validate the test

After having the initial context, I define the actions using my Triggers' Logic (which is like a simplified tool to control execution using Game Objects) in order to create the test.

The Triggers' Logic is something I made to create logic composing GameObjects. I wrote a [blog post at Gemserk](https://blog.gemserk.com/2017/03/27/playing-with-starcraft-2-editor-to-understand-how-a-good-rts-is-made/) explaining where the inspiration came from and its first iteration I did for Iron Marines. My current solution is pretty similar to that one.

For example, the actions for the previous test case could be: 

  1. Press the fire button.
  2. Wait some time.
  3. Check kunai is moving up.

<div class="post-image">
<video width="100%" controls>
  <source src="/assets/tdd/tdd-testcase-manual-validation-noimplemenetation.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>Shows the test case running but no logic yet</span>
</div>

After some magical implementation with physics, trigger callbacks and changing velocities, I have the test working as I expected:

<div class="post-image">
<video width="100%" controls>
  <source src="/assets/tdd/tdd-testcase-manual-validation.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>And now with the code working.</span>
</div>

_After having the initial test, I wanted another to see how the redirect behaves in different directions and... what happens if I make a loop?_

<div class="post-image">
<video width="100%" controls>
  <source src="/assets/tdd/nekoplatformer-redirect-hipnotic.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>Redirect kunai feature</span>
</div>

## Refactoring

After the test is working, I normally do some refactoring for the code and for the data, and having the test to validate everything still works helps a lot. 

I will not enter in detail here but one example data refactor I do is to extract to prefabs the definition and the level design element. An example code refactor I do is to move logic [from a scripts to systems](/2023/07/13/design-decisions-when-building-games-using-ecs).

_As a side note, I also have "tests" that I use to validate visual effects working as expected and if I like them or not. The cycle here is to modify the effect, play the scene, if I like it, then continue with another thing, if I don't like it, then modify the effect until I do._

<div class="post-image">
<video width="100%" controls>
  <source src="/assets/tdd/tdd-vfx-validation.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>It shows the scene I used to validate a bit the teleport effect</span>
</div>

# Automatization

Even though my test cases have no need of manual interaction, they are not completely automatic since I validate the results manually, for example, watching the kunai being redirected for the previous case. This obviously doesn't scale when having multiple test cases and in different scenes.

## Automatic validation with assert actions

Recently, I started working in filling that missing part of my workflow. To do that I created new assert actions to validate state, for example: "this entity should be around this position". 

In the case of the previous example, the new action asserts the velocity of an entity (the kunai) is between to values, in this case I want the x component to be 0 and y to be positive.

<div class="post-image">
<video width="100%" controls>
  <source src="/assets/tdd/tdd-testcase-automatic-validation.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>Shows the same test running but now automatic validation.</span>
</div>

Now that I have automatic validation there is no need for me to go test by test to run them each time I change something. For that, I need a way to run them all.

## Integration with Unity Test Runner

Unity comes with a Test Runner that allows you to run from simple unit tests to more complex ones that require the runtime initialized and to execute over time.

The objective here is to have my test cases listed in the test runner and when I run them, it should open each scene, run the test, wait for a result and then show it before continuing with next test.

<div class="post-image">
 <img src="/assets/tdd-nekoplatformer-testrunner.png" width="100%" />
<span>A list of tests in Unity's Test Runner based on my custom tests inside the scene.</span>
</div>

NUnit comes with a way to populate tests parameters by using attributes. I am using [`ValueSource`](https://docs.nunit.org/articles/nunit/writing-tests/attributes/valuesource.html) with custom data with the name test, the time scale to use, etc. One good thing about the Test Runner is that automatically [considers this test with parameters](https://docs.unity3d.com/Packages/com.unity.test-framework@1.3/manual/reference-tests-parameterized.html) and shows them in the UI.

Since these are Play Mode tests there are some limitations: my tests scene should be included in the build and the Editor's API can't be used. 

To generate the custom data for the test parameters, during edit time I pre process the tests scenes and create an asset in the Resources folder (in order to be found in runtime) with all test cases custom data.

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

It basically loads the scene where the test case is, initializes test state and variables (used by the Triggers's logic Actions to set test results), finds the test and activates it, waits for a result or a timeout.

One interesting point here is that since I am using FixedUpdate for most of my important logic, it is possible to speed up the execution by modifying the time scale. If something fails, I can always go to the specific test and manually run it to fix the issue. 

<div class="post-image">
<video width="100%" controls>
  <source src="/assets/tdd/tdd-testcase-automatic-validation-testrunner.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>Here is the same test running through the test runner and at 10x.</span>
</div>

# Conclusions

One of the important things of the process of having automatic tests is to generate a context where game content and features can be easily tested (automatically or manually) without requiring the rest of the game loaded and interacting. This indirectly helps in decoupling code and content (and in Unity also prefabs, assets, scenes). To be able to easily create isolated context is super helpful also when replicating bugs to fix them. I have a lot of bug replication tests.  

For me, the important part is the design process when making the tests, not the tests themselves. If a feature changes over time, I don't force myself to maintain tests that don't make sense anymore. 

Another great feature of having tests is documentation. Tests are a great way to remember why you made a decision. Bug replication tests help a lot in this matter too. 

_I remember when we started working on Iron Marines, we went back and forward with a lot of features. At some point of its development we wanted to add a new feature that played against something we've decided like 1 year before but we did't remember the reason. Sometimes we just avoided that, others we went against the original decision only to find out sometime later why we've decided it and then we had a double problem to solve._ 

Automatic testing doesn't replace playing the game and/or testing the feature/content in the proper levels and with the rest of the game content. I have a special play button that allows me to test the full experience by loading the game from the start, in a maximized window, and returns to the scene I was after I press the stop button. I even have cheats to move from one level to the other. 

Also, tests can't replace fine tuning and polishing but having isolated context to work on that speeds up the iteration time a lot. 

To end the blog post I want to show a video of this prototype.

<div class="post-image">
<video width="100%" controls>
  <source src="/assets/tdd/tdd-nekoplatformer-gameplay.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video> 
<span>Just some gameplay of the prototype I am working on.</span>
</div>

Hope you liked it, and if you do, remember to share and like in social media if you want. 

Thanks so much for reading!! 

_And special thanks to my friends [Rubén Garat](https://rgarat.dev/) and [Juan Andrés Nin](https://www.nin.com.uy/) who help me validating the drafts for each blog post and suggest me fixes and improvements._ 