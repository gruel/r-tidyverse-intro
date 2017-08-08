---
layout: page 
title: Challenges and Discussion
permalink: /challenge/
---
<script>
  window.onload = function() {
    var lesson_episodes = [
    {% for episode in site.episodes %}
    "{{ episode.url}}"{% unless forloop.last %},{% endunless %}
    {% endfor %}
    ];
    var xmlHttp = [];  /* Required since we are going to query every episode. */
    for (i=0; i < lesson_episodes.length; i++) {
      xmlHttp[i] = new XMLHttpRequest();
      xmlHttp[i].episode = lesson_episodes[i];  /* To enable use this later. */
      xmlHttp[i].onreadystatechange = function() {
      if (this.readyState == 4 && this.status == 200) {
        var article_here = document.getElementById(this.episode);
        var parser = new DOMParser();
        var htmlDoc = parser.parseFromString(this.responseText,"text/html");
        var htmlDocBlockquote = htmlDoc.getElementsByTagName("blockquote");
		for(j=0; j < htmlDocBlockquote.length; j++) {
			if(htmlDocBlockquote[j].getAttribute("class") == "challenge"){
        		article_here.innerHTML += htmlDocBlockquote[j].outerHTML;
			}
		}
        }
      }
      episode_url = "{{ page.root }}" + lesson_episodes[i];
      xmlHttp[i].open("GET", episode_url);
      xmlHttp[i].send(null);
    }
  }
</script>
{% comment %}
Create anchor for each one of the episodes.
{% endcomment %}
{% for episode in site.episodes %}
<article id="{{ episode.url }}"></article>
{% endfor %}