---
layout: default
title: Max's Posts
---
	<h1>{{ page.title }}</h1>
	<ul class="posts">

	  {% for post in site.posts %}
	    <li> 
                 <a href="{{ post.url }}" title="{{ post.title }}" class="postTitle">{{ post.title }}</a> 
                 <span class="postDate">{{ post.date | date_to_string }}</span>
            </li>
	  {% endfor %}
	</ul>
