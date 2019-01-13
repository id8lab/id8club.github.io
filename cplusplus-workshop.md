---
layout: default
title: C++ Workshop
image: assets/images/cplusplus.gif
permalink: cplusplusworkshop.html
---


<h1>C++ workshop</h1>

<ul>
  {% for item in site.cplusplus_collection %}
    <li>
      <h2>{{ item.title }}</h2>
      <p>{{ item.description }}</p>
      <p><a href="{{ item.url }}">{{ item.title }}</a></p>
    </li>
  {% endfor %}
</ul>