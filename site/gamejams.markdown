---
layout: page
title: Jams
permalink: /jams/
projects:
  - name: Spice Must Flow
    url: https://arielsan.itch.io/spice-must-flow
    image: /images/jams_screenshots_05.gif
    description: Harvest Dune's Spice while surviving Sand Worm attacks.
    jam: LDJAM52
  - name: Seedcity Chasers
    url: https://arielsan.itch.io/seedcity-chasers
    image: /images/jams_screenshot_03.gif
    description: TODO
    jam: GBJAM10
  - name: Nekosama
    url: https://arielsan.itch.io/neko-sama
    image: /images/jams_screenshot_01.gif
    description: TODO
    jam: GBJAM9
  - name: Orbital Wars
    url: https://arielsan.itch.io/orbital-wars
    image: /images/jams_screenshot_02.gif
    description: TODO.
    jam: GBJAM7
  - name: Bankin' Bacon
    url: https://gemserk.itch.io/bankinbacon
    image: /images/jams_screenshots_04.png
    description: TODO
    jam: LDJAM44

---

<!-- 
The idea of this page is to highlight some of the projects I've worked on, stuff that I am proud to show
* Could explain a bit each game, one liner.
 -->

This page shows some of the games I made for game jams like Ludum Dare, Global Game Jam and also Gameboy Jam.

You can check my latest game jams entries at my <a href="{{site.itchio_url}}">itch.io</a> page (and <a href="https://blog.gemserk.com/games/">Gemserk</a> for the old ones).

<p>

{%- for project in page.projects -%}
<div class="project">
    <a href="{{project.url}}"><img src="{{project.image}}" /></a>
    <span><a href="{{project.url}}">{{project.name}}</a>: {{project.description}} #{{project.jam}}</span>
</div>
{%- endfor -%}

</p>