---
layout: post
title:  "Steam Next Fest results and plan to get into Early Access in November"
date:   2026-10-30 00:08:00 -0300
excerpt: Ship Miner Demo was part of the last Steam Next Fest (Oct 2026) and this post is about how it performed and my plan towards the Early Access Release on November 26th.
author: Ariel Coppes
tags:
  - shipminer
  - update
  - review
  - personal
  - earlyaccess
  - wishlists
image:
  path: /images/shipminer-preview.png
  height: 100 
  width: 100
---

Ship Miner Demo was part of the last [Steam Next Fest (Oct 2026)](https://store.steampowered.com/sale/nextfest) and this post is about how it performed and my plan towards the Early Access Release on November 26th.

<div class="post-image">
  <img src="/assets/shipminer/nextfest/shipminer-in-nextfest.png" width="100%" />
</div>

This Steam next fest started in a random mode where all games get similar visibility. I understand that the only thing that could affect visibility was which languages are supported by the game, for example, if I only speak english and spanish, games that don't support neither of those languages will be less visible to me than games that support at least one of them. They switched to a personalized mode on the third day, showing games related to what you like, and start considering which games are doing better during the fest. 

<div class="post-image">
  <img src="/assets/shipminer/nextfest/steam-next-fest-explorationmode.png" width="100%" />
</div>

For Ship Miner, the fest didn't start well, I was checking numbers after hours and had almost no wishlists and no players playing the demo. I suppose its visibility was bad considering how many games were in the fest this time and I thought for a moment that it was going to be a really really bad fest.

During the first day I was lucky enough that [Indie Game Joe](https://indiegamejoe.com/) did [a post about Ship Miner](https://x.com/IndieGameJoe/status/2112327036060545216) on multiple social networks and that got a good visibility push which also generated wishlists. I'm not sure if that affected the steam next fest performance but it was good to see it to balance the bad start. 

<div class="post-image">
  <img src="/assets/shipminer/nextfest/tweet-indiegamejoe.png" width="75%" />
  <span>Thanks you so much Joe :raised_hands:!!</span>
</div>

After the third day the game started to get good amount of impressions which also generated wishlists, and even though my expectations were higher before the fest, it ended up being a good result considering the bad start and that there were so many games.

<div class="post-image">
  <img src="/assets/shipminer/nextfest/steam-next-fest-results.png" width="100%" />
</div>

The steam results card show 1118 unique players that played the Demo during the fest and a total of 1392 new wishlists during the event (this could be mixed with joe's post) with the intersection of 306 that played the demo and wishlisted the game.

During the fest I used the [How to market a game](https://howtomarketagame.com/) SNF tool to see the projections per day, here is the last day data:

<div class="post-image">
  <img src="/assets/shipminer/nextfest/snf-tool-final.png" width="100%" />
</div>

And if interested in it, here is the HTMAG [results card](/assets/shipminer/nextfest/ship-miner-snf-score.png).

Best thing about the fest was how many people tried the demo and shared feedback both in discord and in the feedback form. And based on this and my own feedback and conclusions about the game, I decided to adjust some things towards the release:

* Game feels good but needs more content (ships, drones, build stations, enemies, technologies, etc).
* Base building is lost when traveling.
* Combat doesn't feel good enough.
* Some players just want to chill out, no combat.
* To be able to uninstall technologies (this one is a bit tricky, more detail later).
* End of game is not so clear and/or not so epic.
* Some reported bugs.

## Plan for Early Access

The main change is to remove the asteroid travel during runs and to play in one asteroid the full run. The main reason behind it is because I couldn't get more depth with the asteroid travel the way I want it and I believe it will help with the feeling of progression of the asteroid itself with the base building and others.

The game will start by selecting different game modes and different asteroid sizes, some of them unlocked by playing the game which will be part of the metagame progress.

<div class="post-image">
  <img src="/assets/shipminer/nextfest/shipminer-different-modes.gif" width="100%" />
</div>

Since I am removing the asteroid travel, the run progression will be affected. To balance that, the idea is to move some of the asteroid travel progression to inside the asteroid and use the anomaly for that. My plan is that each time you defeat an anomaly (or it is alive for more than some time), it does a game changing effect and the rules of the universe change. This changes could be for example less visibility, the asteroid is now more resistant or the anomalies get more attacks. And related to this, one idea to explore is that the player chooses among different options. 

What about the Savegame? right now the game saves the progress each time the player travels to the next asteroid. To fix that, the idea is to add save and close option to keep the asteroid progress but it will not be that easy so I might end up adding that feature during early access and not before.

For the base building, I will try to add more stations, improve how the drones work (mainly the explorer), and add support for different spots not just around the main base. I am still undecided if it is going to be a free mode or there are different spots in the map and you can build around them.

For the end of game I don't have an exact idea but want to change it to be more like a unique moment where the anomaly disappears and then reappears big in the center of the asteroid and starts growing and sucking everything and the player needs to do something special like throwing the artifact inside or just survive enough time to then escape the asteroid.

About game modes, I will release with three modes, the base one which is Stop the anomaly, a Chill out one without enemies and a Custom mode where the player can adjust different things.

<div class="post-image">
  <img src="/assets/shipminer/nextfest/shipminer-custom-mode.gif" width="100%" />
</div>

About the content in general, I will add a new ship and new technologies, not too many but some newer ones to have something new for Demo and Playtest players. New modes are also part of the new content btw.

For the combat, I still believe the game is not a combat game first, so I will keep working on having ways to make it more indirect through other elements but keeping it enjoyable.

Finally, for the uninstall technologies request, I really like this idea, I feel it is related with the sci-fi/spaceship theme and I will probably work on that during EA, but before I will just adjust/modify the technologies that people want to uninstall since I believe the main issue could be these are not so enjoyable. Another reason that makes me delay a bit changing this too much is that the game is a roguelike, if you don't like some technology just don't pick it the next time you play. 

### What about EA roadmap to 1.0?

I am still defining what the roadmap to 1.0 is going to be, but I can imagine adding more content (ships, drones, bases, technologies, enemies and game mode customizations), and work on the ship uninstall feature (which I imagine more like having ship modules and you can only install/uninstall stuff in some of them, etc). The idea is to work on the roadmap during the next month too and to present it before the Early Access release so players can decide if they want to be part of the EA or wait for 1.0.

### What about the Demo during EA? 

My current plan is to remove it (maybe a couple of weeks before the release) and to focus only on the EA but in the future could be useful to have for different steam events so I might adjust it and rework it but I don't have a clear plan for it yet.

Thanks for reading! 
And remember to try the Demo and add Ship Miner to your wishlist!

<div align="center">
<iframe src="https://store.steampowered.com/widget/4028800/?utm_source=personalpage&utm_campaign=announcement" frameborder="0" width="646" height="190"></iframe>
</div>