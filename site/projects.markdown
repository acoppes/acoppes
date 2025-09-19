---
layout: page
title: Games
permalink: /projects/
projects:
  - name: Ship Miner (work in progress)
    url: https://shipminer.arielcoppes.dev/
    steam_widget: https://store.steampowered.com/widget/4028800/?utm_source=personalpage&utm_campaign=announcement
    images: /assets/moonminer/shipminer-gameplay-01.gif
    description: 1bit lowres pixelart scifi miner game. (personal project)
  - name: Cleared Hot (work in progress)
    url: https://store.steampowered.com/app/1710820/Cleared_Hot/
    steam_widget: https://store.steampowered.com/widget/1710820/
    images: /images/screenshots_clearedhot.gif
    description: Nostalgic helicopter shooter with satisfying physics and light tactical elements.
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

Here is a list of games I worked on, ordered by most recent first (some work in progress).

<p>

{%- for project in page.projects -%}
<div class="project">
    <div class="title"><a href="{{project.url}}">{{project.name}}</a></div>
    <a href="{{project.url}}">
      <span>

      {%- for image in project.images -%}
        <img src="{{image}}" />
      {%- endfor -%}

      {%- for video in project.videos -%}
      <div class="post-image">
        <video controls>
          <source src="{{video}}" type="video/mp4">
          Your browser does not support the video tag.
        </video> 
      </div>
      {%- endfor -%}

      {%- if project.steam_widget -%}
        <iframe src="{{project.steam_widget}}" frameborder="0" width="646" height="190"></iframe>
      {%- endif -%}

      </span>
    </a>
    <span>{{project.description}}</span>
</div>
{%- endfor -%}

</p>