---
layout: post
title:  "Ship Miner plan update"
date:   2024-05-31 08:00:00 -0300
excerpt: Share about my current time dedication plan for Ship Miner and also what to I am going to focus right now.
author: Ariel Coppes
tags:
  - shipminer
  - update
  - roadmap
  - nontechnical
image:
  path: /images/shipminer-preview.png
  height: 100
  width: 100
---

<!--
{{page.excerpt}}
-->

Until last month, I was working half of my time in [Cleared Hot](https://store.steampowered.com/app/1710820/Cleared_Hot/) (pls wishlist!) and almost the other half of my time for a javascript web/mobile app for a couple of old friends. 

My plan was to start slowly spending more time on my projects (mainly Ship Miner) by reducing the time spent on the app. But, while thinking that, this tweet happened:

<div class="post-image">
    <a href="https://x.com/arielsan/status/1789789080370258069" target="_blank"><img src="/assets/moonminer/shipminer-besttweet-2024_01.png" /></a>
</div>

This helped me deciding to speed up the plan. So I am targeting to be spending 25% on Ship Miner and 25% on the app in the short term but to target to 50/50 between Cleared Hot and Ship Miner (100% focused on games development) in mid term.

The tweet had a lot of positive comments where my main conclusions were that I can feel confident on the art style direction, and also that the mining core mechanic inspire good things on people, they are imagining games over it that they like. Now, I have to see if the game I end up making is the one they want, and if not, they like it anyways. And, that is the part of the path ahead. 

I have to continue defining stuff, right now I have a good core mechanic and it feels really well but it is only a toy and I want a game over it. 

The core loop in my mind is: mine, survive, improve, repeat. But right now I only have the mining part (and maybe a bit of the repeat part xD).

So my next steps are related to complete that core loop, at least a basic version so I can start play testing it to get feedback. 

Right now I am working on some ideas for the improvement part of the core loop. 

Initially I wanted the game to be in separated steps, and a bit random, like Nuclear Throne, where you select mutations between levels, so in here you will be gathering minerals and then jump to next asteroid and in the middle you were going to buy random upgrades with your minerals. But one problem there is that the player can't plan how to use the minerals. For example, if he doesn't have enough minerals to buy an upgrade it will be weird if that upgrade doesn't appear in the next jump.

One thing I noticed by playing the game is that I like to be most of my time mining. So I am changing a bit the approach, maybe the player can improve whenever he wants, just needs to have the upgrade unlocked and research it with minerals. Research takes some time to complete so he can continue mining. So this would be more like in XCOM where you first have to unlock the research (by completing missions), and then have to wait time to get it completed.

How to unlock upgrades? maybe when jumping to next asteroid, the game could present different options for the player to pick. Another idea, maybe more aligned with mining and exploration is that the upgrades unlocks (blueprints) can be found by mining like other resources, could still present the options to select but at least it is might be more integrated with the mining and discovering cycle. This is a path I want to work on.

<div class="post-image">
    <img src="/assets/moonminer/blueprint-found-ups.gif" />
    <span>Just for fun, when testing the idea of showing notifications to tell you that you found a blueprint I added the notification as a child of the ship and forgot it was going to be rotated with it xD.</span>
</div>

The other thing that happened to me when thinking this was that I want the player to mine and consume minerals but upgrades sound like a definitive thing and also they should take some time to research (and probably increase time I have multiple levels of the same upgrade) and can't be so fast that it doesn't make sense to to use time. So the other thing I thought was to have a way to build consumables, using time too but can be less than upgrades and probably fixed. Consumables can be instant effects, like recover some health, speed up boost, or something like that, and can also be effects that last for some time like a drone that helps you mine faster or search for minerals, etc. I want the player to plan which consumables he wants but I also want him to activate them when he needs to. So I need some kind of inventory to store consumables. I like that but also want to keep it as simple as possible. So I have to test this too.

In general, even if I end up picking another model for upgrades and consumables, I want them to be visible elements that interact with the game, giving the advantage in an indirect way maybe, so I prefer consumables like drones than just a speed up potion. 

So yeah, that is where am I right now, exploring the improvement part. As soon as I get some insight of it, I will try to focus on the survive part which will probably be related with enemies appearing from some places and attack you, steal minerals, etc.

Thanks for reading.
