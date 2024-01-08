---
layout: default
---
{::options parse_block_html="true" parse_span_html="true" /}

**Software by Silas Bartha**

![](/assets/images/construction.gif)
BIG CHANGES UNDERWAY THIS PAGE IS SEVERELY NEGLECTED QUICKLY RUN RUN OVER TO MY [GITHUB](https://github.com/exvacuum){: target="_blank" rel="noreferrer"}

Index:
{% for category in site.data.software %}
- [{{ category.category }}](#{{ category.category }})
{% for entry in category.entries %}
    - [{{ entry.title }}](#{{ entry.title | replace: " ", "-" }})
{% endfor %}
{% endfor %}

{% for category in site.data.software %}
## {{ category.category }}
{: #{{ category.category | replace: " ", "-" }} style="text-align:center;"}


---
{% for entry in category.entries %}
### {{ entry.title }}: {{ entry.brief }}
{: #{{ entry.title | replace: " ", "-" }} style="text-align:center;"}
---
<div class="container-flex">

{% if entry.cover_image %}
![]({{ entry.cover_image }}){: 
    class="smooth bordered" 
    style="
        max-width:250px;
        margin-right: 10px;
    "
}
{% endif %}

<div class="container-flex" style="flex-direction: column;">

{{ entry.full }}

{% for link in entry.links %}
![]({{ link.icon }}){: .inline-icon}[{{ link.title }}]({{ link.url }}){% if link.external == true %}{: target="_blank" rel="noreferrer"}{% endif %}
{% endfor %}
{% if entry.page %}
[Read More -->]({{entry.page}})
{% endif %}
</div>
</div>
{% endfor %}
{% endfor %}

