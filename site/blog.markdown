---
layout: page-with-posts
title: Blog
list_title: Posts
permalink: /blog/

gemserk_posts:
  - url: https://blog.gemserk.com/2022/04/24/refactoring-prefabs-and-unity-objects/
    title: Refactoring Data stored in Unity Prefabs, Scenes and other Assets
    excerpt: It explains tools and techniques for refactoring data serialized in assets and prefabs in Unity, super useful when performing also data structure code refactoring and need to keep serialized data working. 
    date: 2022-04-24
  - url: https://blog.gemserk.com/2020/05/26/using-unity-prefabs-and-gameobjects-only-for-data/
    title: Using Unity Prefabs and GameObjects only to store data.
    excerpt: We share how GameObjects and Prefabs can be used for data storage, like ScriptableObjects, but with extra features like componentization, hierarchy, prefab variants, and more.
    date: 2020-05-26
  - url: https://blog.gemserk.com/2017/03/27/playing-with-starcraft-2-editor-to-understand-how-a-good-rts-is-made/
    title: Playing with Starcraft 2 Editor to understand how a good RTS is made
    excerpt: Explains how we created in Unity a GameObject system for Iron Marines inspired in Starcraft 2 Editor to make levels logic with events, conditions and actions. 
    date: 2017-03-27
---

**[Work in progress]**

[Idea] The idea of this page is to start adding some peronal blog posts about stuff, not sure yet if only development stuff or anything.

Previously, I posted my development blog entries at <a href="{{site.dev_blog}}">Gemserk</a>.

{%- if page.gemserk_posts.size > 0 -%}
  <!-- h2 class="post-list-heading">{{ page.list_title | default: "Posts" }}</h2-->
  <ul class="post-list">
    {%- for post in page.gemserk_posts -%}
    <li>
      {%- assign date_format = site.minima.date_format | default: "%b %-d, %Y" -%}
      <span class="post-meta">{{ post.date | date: date_format }}</span>
      <h3>
        <a class="post-link" href="{{ post.url | relative_url }}">
          {{ post.title | escape }} [Gemserk]
        </a>
      </h3>
      {%- if site.show_excerpts -%}
        {{ post.excerpt }}
      {%- endif -%}
    </li>
    {%- endfor -%}
  </ul>

  {%- endif -%}
