---
layout: page
title: Projects
permalink: /projects/
projects:
  - name: Gorilla Jones
    url: https://arielsan.itch.io/gorillajones
    images: /images/screenshots_gorillajones.png
    description: Help Oday the Archaeologist Ape in his Endless Runner Adventure
  - name: Iron Marines Invasion
    url: https://www.ironhidegames.com/Games/ironmarinesinvasion
    images: /images/screenshots_im2.jpg
    description: Sequel to Iron Marines, I worked as a Lead Engineer and Team Leader for part of the project. For Android and iOS, available at Google Play and App Store.
  - name: Iron Marines
    url: https://www.ironhidegames.com/Games/iron-marines
    images: /images/screenshots_im1.jpg
    description: The first RTS for mobile we made, I worked as a Lead Engineer here, responsible for all the game Engine made in Unity. For Android and iOS, available at Google Play and App Store.
  - name: Kingdom Rush Origins
    url: https://www.ironhidegames.com/Games/kingdom-rush-origins
    images: /images/screenshots_kro.jpg
    description: Prequel to Kingdom Rush, this was the second project I worked on as Programmer. For Android and iOS, available at Google Play and App Store.
  - name: Kingdom Rush Frontiers
    url: https://www.ironhidegames.com/Games/kingdom-rush-frontiers
    images: /images/screenshots_krf.jpg
    description: Sequel to Kingdom Rush, this was the first project I worked on as Programmer. For Android and iOS, available at Google Play and App Store.
  - name: Clash of the Olympians
    url: https://www.ironhidegames.com/Games/clash-of-the-olympians
    images: /images/screenshots_clash.png
    description: At Gemserk, we did the Android and iOS port of this game for Ironhide Game Studio, available at Google Play and App Store.
---

This page shows some of my most important games I've worked on.

Here is a list of games I've worked on in the past, ordered by most recent first.

<p>

{%- for project in page.projects -%}
<div class="project">
    <div class="title"><a href="{{project.url}}">{{project.name}}</a></div>
    <a href="{{project.url}}">
      <span>
      {%- for image in project.images -%}
        <img src="{{image}}" />
      {%- endfor -%}
      </span>
    </a>
    <span>{{project.description}}</span>
</div>
{%- endfor -%}

</p>