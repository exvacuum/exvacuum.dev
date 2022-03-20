---
layout: default
---
{::options parse_block_html="true" /}

**Notes & Scraps**

| Tags |
| ---- |
| {{ site | tag_cloud }} |
{: .bordered style="float:right; margin-left: 10px;" }

{% for category in site.categories %}
## {{ category[0] }}
---
{% for post in category[1] %}
-  [{{ post.title }} ({{ post.date | date: '%B %d, %Y' }})]({{ post.url }})
{% endfor %}
{% endfor %}

