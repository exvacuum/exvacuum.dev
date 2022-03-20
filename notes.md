---
layout: default
---
{::options parse_block_html="true" /}

{% include tagcloud.html %}

**Notes & Scraps**


{% for category in site.categories %}
## {{ category[0] }}
---
{% for post in category[1] %}
-  [{{ post.title }} ({{ post.date | date: '%B %d, %Y' }})]({{ post.url }})
{% endfor %}
{% endfor %}

