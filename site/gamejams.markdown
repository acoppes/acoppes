---
layout: page
title: Jams
permalink: /jams/
projects:
  - name: "Abduction101"
    url: https://arielsan.itch.io/abduction101
    images: 
      - /images/jams-lowrezjam-2023-03.gif
      - /images/jams-lowrezjam-2023-01.gif
    description: Learn human abduction in a few steps and take control of the planet. 
    jam: lowrezjam 2023
  - name: "Mini RTS Battle Experience"
    url: https://arielsan.itch.io/rtsgame
    images: 
      - /images/jams_rtsgame-screenshot-01.gif
      - /images/jams_rtsgame-screenshot-02.gif
    description: Survive waves of enemies using micromanagement in this minimalist RTS experience. 
    jam: two week personal jam
  - name: "Hunter Games"
    url: https://arielsan.itch.io/huntersgame
    images: /images/jams_huntersgame-screenshot-01.gif
    description: Be the best hunter and win the competition in order to save your tribe. 
    jam: one week personal jam
  - name: "Stranded: Planet Survivor"
    url: https://arielsan.itch.io/stranded-planet-survivor
    images: /images/jams_survivor-screenshot-01.gif
    description: Left alone on unknown planet, explore and repair your ship to return home.
    jam: one week personal jam
  - name: Spice Must Flow
    url: https://arielsan.itch.io/spice-must-flow
    images: /images/jams_screenshots_05.gif
    description: Harvest Dune's Spice while surviving Sand Worm attacks.
    jam: LDJAM52
  - name: Generic Beat'em up
    url: https://arielsan.itch.io/beatemup
    images: /images/jams_beatemup-screenshot-01.gif
    description: Endless fast paced action game with some beatemup elements. 
  - name: Seedcity Chasers
    url: https://arielsan.itch.io/seedcity-chasers
    images: /images/jams_screenshot_03.gif
    description: Cyberpunk arcade shoot'em up.
    jam: GBJAM10
  - name: Nekosama
    url: https://arielsan.itch.io/neko-sama
    images: /images/jams_screenshot_01.gif
    description: Hades inspired roguelike with ninja cats.
    jam: GBJAM9
  - name: Orbital Wars
    url: https://arielsan.itch.io/orbital-wars
    images: /images/jams_screenshot_02.gif
    description: Advanced wars inspired game with Iron Marines elements as tribute.
    jam: GBJAM7
  - name: Bankin' Bacon
    url: https://gemserk.itch.io/bankinbacon
    images: /images/jams_screenshots_04.png
    description: Local multiplayer versus where your life is currency. Collect coins to survive while attacking coins to win. 
    jam: LDJAM44

---

This page shows some of the games I made for game jams like Ludum Dare, Global Game Jam and also Gameboy Jam.

You can check my latest game jams entries at my <a href="{{site.itchio_url}}">itch.io</a> page (and <a href="https://blog.gemserk.com/games/">Gemserk</a> for the old ones).

Here is the list of games, ordered by most recent first.

<p>

{%- for project in page.projects -%}
<div class="project">
    <div class="title">{{project.name}}</div>
    <a href="{{project.url}}">(click to play)</a>
    <a href="{{project.url}}">
      <span>
      {%- for image in project.images -%}
        <img src="{{image}}" />
      {%- endfor -%}
      </span>
    </a>
    <span>{{project.description}} {% if project.jam %} #{{project.jam}} {% endif %}</span>
</div>
{%- endfor -%}

</p>