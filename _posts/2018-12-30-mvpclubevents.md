---
layout: post
title: JCU MVP Club Events
author: hansen
comments: true
date:   2018-12-30 23:30:00 +0530
featured: true
published: true
categories: [ mvpclub, jcu ]
image: assets/images/jcucairns.jpg
permalink: jcumvpclubevents.html
---

{% for item in site.events_collection %}
  <h2>{{ item.title }}</h2>
  <p>{{ item.description }}</p>
  <p><a href="{{ item.url }}">{{ item.title }}</a></p>
{% endfor %}


### The next MVP Club session

We will be located in room C4-04
every Thursday, starting at 5:00PM until around 7:30PM
