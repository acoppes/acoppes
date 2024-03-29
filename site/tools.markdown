---
layout: page
title: Tools
permalink: /tools/
projects:
  - name: Unity History Window
    url: https://github.com/acoppes/unity-history-window
    image: /images/tools-selection-history.gif
    description: Editor window plugin for Unity with a selection history and other features. 
  - name: Unity Refactor Tools
    url: https://github.com/acoppes/unity-refactoring-tools
    image: /images/tools-refactoring.png
    description: A set of tools to help refactoring prefabs, scenes and other assets. 
  - name: Unity package Git pusher
    url: https://github.com/acoppes/unity-upm-gitpackagepusher
    image: /images/tools-upm-gitpusher.png
    description: Given a folder with the unity package layout, this Unity plugin will allow you to automatically push a version of it to Github and create specific branches and tags with only its contents in order to use it later as a upm dependency for another Unity project.
  - name: GameObject Paint Tool
    url: https://github.com/acoppes/unity-gameobject-brush
    image: /images/tools-gameobject-brush.gif
    description: Plugin for Unity with a palette of objects and a brush tool to paint them in the scene. 
---

This page shows some of the tools I've created or collaborated with, find more at my <a href="https://github.com/{{ site.github_username| cgi_escape | escape }}?tab=repositories"><span class="username">Github repositories</span></a> page. Some of my plugins are released to [openupm](https://openupm.com/).

<p>

{%- for project in page.projects -%}
<div class="project">
    <a href="{{project.url}}"><img src="{{project.image}}" /></a>
    <span><a href="{{project.url}}">{{project.name}}</a>: {{project.description}}</span>
</div>
{%- endfor -%}

</p>

