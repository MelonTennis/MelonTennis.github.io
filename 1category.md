---
layout: page
title: Learning Centre🍪
description: "人生得意须尽欢 | My heart is in the work"
header-img: "img/twitter.jpg"
permalink: /category/
---

###&ensp;&ensp;Content

<div class="page clearfix">

    <div class="right">
        <div class="col-lg-10 col-lg-offset-0 col-md-10 col-md-offset-1">
            Category
        </div>
        <div class="wrap">
            <!-- Content -->
            <div class="side-category">
                <ul id="content-side" class="content-ul">
                    {% for category in site.categories%}
                    <li>
                        <a class="scroll" href="#{{ category | first }}">
                            {{ category | first }} ({{ category | last | size }})
                        </a>
                    </li>
                    {% endfor %}
                </ul>
            </div>
        </div>
    </div>
    <div class="left">
        <hr>
        <ul>
            {% for category in site.categories %}
            <h2 id="{{category | first}}">{{category | first}}</h2>
                {% for posts in category  %}
                    {% for post in posts %}
                        {% if post.url %}
                        <li>
                            <time>
                            {{ post.date | date:"%F" }} {{ post.date | date: "%a" }}.
                            </time>
                            <a class="title" href="{{ post.url }}">{{ post.title }}</a>
    
                            {% include category.html %}
                            {% include tag.html %}
                        </li>
                        {% endif %}
                    {% endfor %}
                {% endfor %}
            {% endfor %}
        </ul>
    </div>

</div>
<script src="{{ "/js/pageContent.js " | prepend: site.baseurl }}" charset="utf-8"></script>
