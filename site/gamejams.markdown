---
layout: page
title: Jams
permalink: /jams/
projects:
  - name: "Hunter Games"
    url: https://arielsan.itch.io/huntersgame
    image: /images/jams_huntersgame-screenshot-01.gif
    description: Be the best hunter and win the competition in order to save your tribe. 
    jam: one week personal jam
  - name: "Stranded: Planet Survivor"
    url: https://arielsan.itch.io/stranded-planet-survivor
    image: /images/jams_survivor-screenshot-01.gif
    description: Left alone on unknown planet, explore and repair your ship to return home.
    jam: one week personal jam
  - name: Spice Must Flow
    url: https://arielsan.itch.io/spice-must-flow
    image: /images/jams_screenshots_05.gif
    description: Harvest Dune's Spice while surviving Sand Worm attacks.
    jam: LDJAM52
  - name: Seedcity Chasers
    url: https://arielsan.itch.io/seedcity-chasers
    image: /images/jams_screenshot_03.gif
    description: Cyberpunk arcade shoot'em up.
    jam: GBJAM10
  - name: Nekosama
    url: https://arielsan.itch.io/neko-sama
    image: /images/jams_screenshot_01.gif
    description: Hades inspired roguelike with ninja cats.
    jam: GBJAM9
  - name: Orbital Wars
    url: https://arielsan.itch.io/orbital-wars
    image: /images/jams_screenshot_02.gif
    description: Advanced wars inspired game with Iron Marines elements as tribute.
    jam: GBJAM7
  - name: Bankin' Bacon
    url: https://gemserk.itch.io/bankinbacon
    image: /images/jams_screenshots_04.png
    description: Local multiplayer versus where your life is currency. Collect coins to survive while attacking coins to win. 
    jam: LDJAM44

---

This page shows some of the games I made for game jams like Ludum Dare, Global Game Jam and also Gameboy Jam.

You can check my latest game jams entries at my <a href="{{site.itchio_url}}">itch.io</a> page (and <a href="https://blog.gemserk.com/games/">Gemserk</a> for the old ones).

Here is the list of games, ordered by most recent first.

<p>

{%- for project in page.projects -%}
<div class="project">
    <a href="{{project.url}}"><img src="{{project.image}}" /></a>
    <span><a href="{{project.url}}">{{project.name}}</a>: {{project.description}} #{{project.jam}}</span>
</div>
{%- endfor -%}

</p>