---
layout: page
title: Projects
permalink: /projects/
projects:
  - name: Iron Marines Invasion
    url: https://www.ironhidegames.com/Games/ironmarinesinvasion
    image: /images/screenshots_im2.jpg
    description: Sequel to Iron Marines, I worked as a Lead Engineer and Team Leader for part of the project.
  - name: Iron Marines
    url: https://www.ironhidegames.com/Games/iron-marines
    image: /images/screenshots_im1.jpg
    description: The first RTS for mobile we made, I worked as a Lead Engineer here, responsible for all the game Engine made in Unity.
  - name: Kingdom Rush Origins
    url: https://www.ironhidegames.com/Games/kingdom-rush-origins
    image: /images/screenshots_kro.jpg
    description: Prequel to Kingdom Rush, this was the second project I worked on as Programmer.
  - name: Kingdom Rush Frontiers
    url: https://www.ironhidegames.com/Games/kingdom-rush-frontiers
    image: /images/screenshots_krf.jpg
    description: Sequel to Kingdom Rush, this was the first project I worked on as Programmer.
  - name: Clash of the Olympians
    url: https://www.ironhidegames.com/Games/clash-of-the-olympians
    image: /images/screenshots_clash.png
    description: At Gemserk, we did the Android and iOS port of this game for Ironhide Game Studio.
---

<!-- 
The idea of this page is to highlight some of the projects I've worked on, stuff that I am proud to show
* Could explain a bit each game, one liner.
 -->

This page shows some of my most important projects I've worked on professionally.

Find my open source projects and tools and other collaborations at my personal <a href="https://github.com/{{ site.github_username| cgi_escape | escape }}"><span class="username">Github</span></a> page. For example, I have a plugin for Unity, released to [openupm](https://openupm.com/), to store and show the user's selection history, check it [here](https://github.com/acoppes/unity-history-window).

Here is the list of games, ordered by most recent first.

<p>

{%- for project in page.projects -%}
<div class="project">
    <a href="{{project.url}}"><img src="{{project.image}}" /></a>
    <span><a href="{{project.url}}">{{project.name}}</a>: {{project.description}}</span>
</div>
{%- endfor -%}

</p>