---
layout: post
title:  "Ship Miner now supports multiple languages!"
# date:   2026-08-23 00:08:30 -0300
excerpt: Even though the game is not finished, and will change for sure, preparing the game to support different languages shouldn't be the last thing to do.
author: Ariel Coppes
tags:
  - development
  - gamedesign
  - metagame
image:
  path: /images/ecs-post-preview.jpg
  height: 100
  width: 100
---

<div class="post-image">
  <img src="/assets/shipminer/shipminer-gameplay_02.gif" width="100%" />
  <span>This is Ship Miner btw, <a href="https://store.steampowered.com/app/3113690/Ship_Miner/?utm_source=personalpage&utm_campaign=devlog">Demo available on Steam!</a></span>
</div>

{{page.excerpt}}

The objective for the last week was to technically prepare the game while also validate some UI changes I did a couple of months ago where I decided to change to use a pixelart font named [Silver](https://poppyworks.itch.io/silver) that supports characters from multiple languages. The texts for each language are stored in a language file and I used the typical `"key"="value"` format.

Long story short, the results were really good, the font seems to support pretty well Japanese, Chinese, Korean and Russian. From the UI side, I had to adjust some UI text elements pivots/anchors so the texts are centered properly and the size of the containers but in general it was relatively easy (although it might require still more work I didn't notice). I can say that the work I did on the UI some time ago to support the new font payed off now.

After testing the game in other languages, maybe the only thing I might change are the ingame notifications which now look a bit too big considering the new font, maybe I change all notifications to be in HUD but I will wait to test it a bit more before deciding that.

One thing that happened was that now that the game seems to easily support other languages, I want it to be released in as many as possible. But I can't pay for all translations, so my current plan is to do the texts for both English and Spanish myself (might pay for proof reading) and pay for Japanese, Chinese and Russian translations. And for the future I might continue with Portuguese, Korean, German, French and Italian.

I also made this easy to extend by the community since adding new languages is as easy as adding a text file in a runtime special folder and the game autodetects it and shows it in the language selection window. I would love for the community to add new language files, test them and if they are good and used by other players, then include them as part of the build, and obviously to the credits (talking about that, I still need to create the credits window).

To validate the game changes for the main languages I want to support, I used the manual Spanish translation and autotranslated versions for the other languages. For the autotranslated ones, I don't know what they say and/or if they are right or not but it helped me to validate features. My plan is to work with a localization team but I am thinking about adding command line option to show the autotranslated versions in the language selection window just in case someone wants to play with them anyways (might also show a warning when selecting them).

<div class="post-image">
  <img src="/assets/shipminer/localization/shipminer-localization-jptest.gif" width="100%" />
  <span>Here is how the game could look in Japanese.</span>
</div>

There are still things to be done to completely support for other languages, for example adjusting upgrade texts based on the selected ship and/or current upgrade level. My plan is to try to close a couple of things and make a new release for playtesters.

After that, my next iteration will be on unlocking content during the game to start having a glimpse of how that feels and the impact in the game, focusing on the objective of making the game feel more different between runs.

I can already start talking with the localization team and start adjusting my plan, I now have a better word count (~1000 right now, but could go to 2000) that helps when calculating the cost and evaluate against my budget. 

I am really happy with how the game looks in other languages already, it feels more like a real product. I really hope more players can enjoy the game now.

<div align="center">
<iframe src="https://store.steampowered.com/widget/3113690/?utm_source=personalpage&utm_campaign=devlog" frameborder="0" width="646" height="190"></iframe>
</div>

Thanks so much for reading and remember to play the demo and/or wishlist the game!
