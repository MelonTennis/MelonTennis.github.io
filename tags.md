---
title: Tags
layout: page2
description: "春风又绿江南岸 | Less is more"
header-img: "img/cmuposter.jpg"
---

### **· TAGS ～ 🏷️列表**

<!-- Main Content -->
<div class="container">
<div class="row">

<div class="col-lg-8 col-lg-offset-0 col-md-10 col-md-offset-1">
  <!-- 标签云 -->
  	<div id='tag_cloud' class="tags" style="color:#6495ED size = 7">

  		{% for tag in site.tags %}

            <a href="#{{ tag[0] }}" title="{{ tag[0] }}" rel="{{ tag[1].size }}">{{ tag[0] }}</a>
  		{% endfor %}
  	</div>


            <!-- 标签列表 -->
    		{% for tag in site.tags %}
    		<div class="one-tag-list">
    		  	<span class="fa fa-tag listing-seperator" id="{{ tag[0] }}">
                    <span class="tag-text">{{ tag[0] }}</span>
                </span>
    			{% for post in tag[1] %}
    			  <!-- <li class="listing-item">
    			  <time datetime="{{ post.date | date:"%Y-%m-%d" }}">{{ post.date | date:"%Y-%m-%d" }}</time>
    			  <a href="{{ post.url }}" title="{{ post.title }}">{{ post.title }}</a>
    			  </li> -->
    			 <div class="post-preview">
    			    <a href="{{ post.url | prepend: site.baseurl }}">
    			        <h2 class="post-title">
                            {{ post.title }}
    			        </h2>
    			        {% if post.subtitle %}
    			        <h3 class="post-subtitle">
    			            {{ post.subtitle }}
    			        </h3>
    			        {% endif %}
    			    </a>
    			    <!-- <p class="post-meta">{{ post.date | date:"%Y-%m-%d" }}</p> -->
    			</div>
    			<hr>
    			{% endfor %}
    		</div>
    		{% endfor %}
    
    	</div>
    </div>
</div>
