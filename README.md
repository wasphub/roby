# roby
{% for post in site.posts %}
[{{ post.title }}]({{ site.url }}{{ site.baseurl }}{{ post.url }})
{% endfor %}
